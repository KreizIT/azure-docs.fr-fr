---
title: Déplacement d’un coffre Azure Key Vault vers un autre abonnement | Microsoft Docs
description: Conseils pour le déplacement d’un coffre de clés vers un autre abonnement.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e1e4dd244045e86a0fb9e6d65f81a4149cf6659
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413300"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Déplacement d’un coffre Azure Key Vault vers un nouvel abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Vue d’ensemble

> [!IMPORTANT]
> **Le déplacement d’un coffre de clés vers un autre abonnement entraîne un changement radical de votre environnement.**
> Veillez à bien comprendre l’impact de ce changement et suivez attentivement les conseils prodigués dans cet article avant de décider de déplacer le coffre de clés vers un nouvel abonnement.
> Si vous utilisez des identités de service managées (MSI), lisez les instructions postérieures au déplacement à la fin de ce document. 

[Azure Key Vault](overview.md) est automatiquement lié à l’ID de locataire [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) par défaut pour l’abonnement dans lequel il est créé. Vous pouvez trouver l’ID de locataire associé à votre abonnement en suivant ce [guide](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md). Toutes les entrées de stratégie d’accès les attributions de rôle sont également liées à cet ID de locataire.  Si vous déplacez votre abonnement Azure d’un locataire A vers un locataire B, vos coffres de clés existants ne sont pas accessibles par les principaux du service (utilisateurs et applications) dans le locataire B. Pour résoudre ce problème, vous devez :

> [!NOTE]
> Si Key Vault est créé par l’intermédiaire d’[Azure Lighthouse](../../lighthouse/overview.md), il est lié à la gestion de l’ID de locataire à la place. Azure Lighthouse est uniquement pris en charge par le modèle d’autorisation de stratégie d’accès de coffre.
> Pour plus d’informations sur les locataires dans Azure Lighthouse, consultez [Locataires, utilisateurs et rôles dans Azure Lighthouse](../../lighthouse/concepts/tenants-users-roles.md).

* remplacer l’ID de locataire associé à tous les coffres de clés existants dans l’abonnement par le locataire B ;
* supprimer toutes les entrées de stratégie d’accès existantes ;
* ajouter de nouvelles entrées de stratégie d’accès associées au locataire B.

Pour plus d’informations sur Azure Key Vault et Azure Active Directory, consultez :
- [À propos d’Azure Key Vault](overview.md)
- [Qu’est-ce qu’Azure Active Directory ?](../../active-directory/fundamentals/active-directory-whatis.md)
- [Guide pratique pour rechercher un ID de locataire](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Limites

> [!IMPORTANT]
> **Les coffres de clés utilisés pour le chiffrement de disque ne peuvent pas être déplacés** : si vous utilisez un coffre de clés avec le chiffrement de disque pour une machine virtuelle, le coffre de clés ne peut pas être déplacé vers un autre groupe de ressources ou un abonnement lorsque le chiffrement de disque est activé. Vous devez désactiver le chiffrement de disque avant de déplacer le coffre de clés vers un nouveau groupe de ressources ou un nouvel abonnement. 

Certains principaux du service (utilisateurs et applications) sont liés à un locataire spécifique. Si vous déplacez votre coffre de clés vers un abonnement dans un autre locataire, vous risquez de ne pas pouvoir restaurer l’accès à un principal du service spécifique. Vérifiez que tous les principaux du service essentiels existent dans le locataire vers lequel vous déplacez votre coffre de clés.

## <a name="prerequisites"></a>Prérequis

* Accès de niveau [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) ou supérieur à l’abonnement actuel dans lequel se trouve votre coffre de clés. Vous pouvez attribuer un rôle à l’aide de [Portail Azure](../../role-based-access-control/role-assignments-portal.md), d’[Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou de [PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Accès de niveau [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) ou supérieur à l’abonnement dans lequel vous souhaitez déplacer votre coffre de clés. Vous pouvez attribuer un rôle à l’aide de [Portail Azure](../../role-based-access-control/role-assignments-portal.md), d’[Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou de [PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Groupe de ressources dans le nouvel abonnement. Vous pouvez en créer un à l’aide de [Portail Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md), de [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md) ou d’[Azure CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md).

Vous pouvez vérifier les rôles existants à l’aide de [Portail Azure](../../role-based-access-control/role-assignments-list-portal.md), de [PowerShell](../../role-based-access-control/role-assignments-list-powershell.md), d’[Azure CLI](../../role-based-access-control/role-assignments-list-cli.md) ou de l’[API REST](../../role-based-access-control/role-assignments-list-rest.md).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Déplacement d’un coffre de clés vers un nouvel abonnement

1. Connectez-vous au portail Azure sur https://portal.azure.com.
2. Accédez à votre [coffre de clés](overview.md).
3. Cliquer sur l’onglet « Vue d’ensemble »
4. Sélectionnez le bouton « Déplacer ».
5. Dans la liste déroulante, sélectionnez l’option « Déplacer vers un autre abonnement ».
6. Sélectionnez le groupe de ressources vers lequel vous souhaitez déplacer votre coffre de clés.
7. Confirmer la lecture de l’avertissement sur le déplacement des ressources
8. Sélectionner « OK »

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Étapes supplémentaires lorsque l’abonnement est dans un nouveau locataire

Si vous avez déplacé votre coffre de clés vers un abonnement associé à un nouveau locataire, vous devez manuellement mettre à jour l’ID du locataire et supprimer les anciennes stratégies d’accès et attributions de rôle. Voici des didacticiels pour ces étapes dans PowerShell et Azure CLI. Si vous utilisez PowerShell, vous devrez peut-être exécuter la commande Clear-AzContext documentée ci-dessous pour pouvoir visualiser les ressources en dehors de l’étendue sélectionnée actuelle. 

### <a name="update-tenant-id-in-a-key-vault"></a>Mettre à jour l’ID de locataire dans un coffre de clés

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
$tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Mettre à jour les stratégies d’accès et les attributions de rôles

> [!NOTE]
> Si Key Vault utilise un modèle d’autorisation [Azure RBAC](../../role-based-access-control/overview.md). Vous devez également supprimer les attributions de rôle de coffre de clés. Vous pouvez supprimer des attributions de rôles à l’aide de [Portail Azure](../../role-based-access-control/role-assignments-portal.md), d’[Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou de [PowerShell](../../role-based-access-control/role-assignments-powershell.md). 

Maintenant que votre coffre est associé à l’ID de locataire qui convient et que les anciennes entrées de stratégie d’accès ou attributions de rôle sont supprimées, définissez les nouvelles entrées de stratégie d’accès ou attributions de rôle.

Pour attribuer des stratégies, consultez :
- [Attribuer une stratégie d’accès à l’aide du portail](assign-access-policy-portal.md)
- [Attribuer une stratégie d’accès à l’aide d’Azure CLI](assign-access-policy-cli.md)
- [Attribuer une stratégie d’accès à l’aide de PowerShell](assign-access-policy-powershell.md)

Pour ajouter des attributions de rôles, consultez :
- [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md)
- [Attribuer des rôles Azure à l’aide d’Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Attribuer des rôles Azure à l’aide de PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Mettre à jour les identités managées

Si vous transférez un abonnement entier et que vous utilisez une identité managée pour les ressources Azure, vous devez également la mettre à jour vers le nouveau locataire Azure Active Directory. Pour plus d’informations sur les identités managées, consultez [Vue d’ensemble des identités managées](../../active-directory/managed-identities-azure-resources/overview.md).

Si vous utilisez une identité managée, vous devrez également mettre à jour l’identité, car l’ancienne identité ne se trouvera plus dans le bon locataire Azure Active Directory. Consultez les documents suivants qui vous aideront à résoudre ce problème. 

* [Mise à jour des identités de service managées](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Transférer un abonnement vers un nouveau répertoire](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [clés, secrets et certificats](about-keys-secrets-certificates.md)
- Pour obtenir des informations conceptuelles, notamment sur l’interprétation des journaux Key Vault, consultez [Journalisation de Key Vault](logging.md).
- [Guide du développeur Key Vault](../general/developers-guide.md)
- [Fonctionnalités de sécurité pour Azure Key Vault](security-features.md)
- [Configurer les pare-feux et réseaux virtuels d’Azure Key Vault](network-security.md)
