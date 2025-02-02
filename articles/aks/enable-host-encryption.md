---
title: Activer le chiffrement basé sur l’hôte sur Azure Kubernetes Service (AKS)
description: Découvrez comment configurer un chiffrement basé sur l’hôte dans un cluster Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 04/26/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3eb2f0023cbd0bbe36b466ecf4a1380aa20a2c5c
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527970"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks"></a>Chiffrement basé sur l’hôte sur Azure Kubernetes Service (AKS)

Avec le chiffrement basé sur l’hôte, les données stockées sur l’hôte de machine virtuelle des machines virtuelles de vos nœuds d’agent AKS sont chiffrées au repos et les flux sont chiffrés dans le service de stockage. Cela signifie que les disques temporaires sont chiffrés au repos avec des clés gérées par la plateforme. Le cache du système d’exploitation et des disques de données est chiffré au repos avec des clés gérées par le client ou par la plateforme, selon le type de chiffrement défini sur ces disques. Par défaut, lorsque vous utilisez AKS, le système d’exploitation et les disques de données sont chiffrés au repos avec des clés gérées par la plateforme, ce qui signifie que les caches de ces disques sont également chiffrés au repos par défaut avec des clés gérées par la plateforme.  Vous pouvez spécifier vos propres clés gérées à l’aide de [Bring your own keys (BYOK) avec des disques Azure dans Azure Kubernetes service](azure-disk-customer-managed-keys.md). Le cache de ces disques sera également chiffré à l’aide de la clé que vous spécifiez au cours de cette étape.


## <a name="before-you-begin"></a>Avant de commencer

Cette fonctionnalité ne peut être définie qu’au moment de la création du cluster ou de la création du pool de nœuds.

> [!NOTE]
> Le chiffrement basé sur l’hôte est disponible dans les [régions Azure][supported-regions] qui prennent en charge le chiffrement côté serveur des disques managés Azure et uniquement avec des [tailles de machine virtuelle spécifiques prises en charge][supported-sizes].

### <a name="prerequisites"></a>Prérequis

- Vérifiez que l’extension CLI version 2.23 ou ultérieure est installée.
- Vérifiez que l’indicateur de fonctionnalité `EncryptionAtHost` sous `Microsoft.Compute` est activé.

### <a name="register-encryptionathost--preview-features"></a>Enregistrer `EncryptionAtHost` fonctionnalités d’évaluation

Pour créer un cluster AKS qui utilise un chiffrement basé sur l’hôte, vous devez activer les indicateurs de fonctionnalité `EncryptionAtHost` sur votre abonnement.

Inscrivez l’indicateur de fonctionnalité `EncryptionAtHost` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l’état de l’enregistrement à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources `Microsoft.Compute` à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

### <a name="limitations"></a>Limites

- Ne peut être activé que sur les nouveaux pools de nœuds.
- Ne peut être activé que dans les [régions Azure][supported-regions] qui prennent en charge le chiffrement côté serveur des disques managés Azure et uniquement avec des [tailles de machine virtuelle spécifiques prises en charge][supported-sizes].
- Requiert un cluster AKS et un pool de nœuds basés sur Virtual Machine Scale Sets (VMSS) comme *type d’ensemble de machines virtuelles*.

## <a name="use-host-based-encryption-on-new-clusters"></a>Utiliser le chiffrement basé sur l’hôte sur les nouveaux clusters

Configurez les nœuds de l’agent de cluster pour utiliser le chiffrement basé sur l’hôte lorsque le cluster est créé. 

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Si vous souhaitez créer des clusters sans chiffrement basé sur l'hôte, vous pouvez le faire en omettant le paramètre `--enable-encryption-at-host`.

## <a name="use-host-based-encryption-on-existing-clusters"></a>Utiliser le chiffrement basé sur l’hôte sur les clusters existants

Vous pouvez activer le chiffrement basé sur l’hôte sur les clusters existants en ajoutant un nouveau pool de nœuds à votre cluster. Configurez un nouveau pool de nœuds pour utiliser le chiffrement basé sur l'hôte à l'aide du paramètre `--enable-encryption-at-host`.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --enable-encryption-at-host
```

Si vous souhaitez créer de nouveaux pools de nœuds sans la fonctionnalité de chiffrement basé sur l'hôte, vous pouvez le faire en omettant le paramètre `--enable-encryption-at-host`.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue les [meilleures pratiques pour la sécurité du cluster AKS][best-practices-security] En savoir plus sur le [chiffrement basé sur l’hôte](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
