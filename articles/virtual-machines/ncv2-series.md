---
title: Série NCv2 – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles de la série NCv2.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d87aac528491a7360ae857be7f959eb66b44845d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003368"
---
# <a name="ncv2-series"></a>Série NCv2

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les machines virtuelles de série NCv2 sont optimisées par les GPU NVIDIA Tesla P100. Ces GPU peuvent fournir des performances de calcul deux fois supérieures à celles de la série NC. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. Outre les GPU, les machines virtuelles de la série NCv2 sont également dotées de processeurs Intel Xeon E5-2690 v4 (Broadwell).

La configuration NC24rs v2 fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.

[Stockage Premium](premium-storage-performance.md) : Pris en charge<br>
[Mise en cache du Stockage Premium](premium-storage-performance.md) : Pris(e) en charge<br>
[Disques Ultra](disks-types.md#ultra-disks) : pris en charge ([En savoir plus](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) sur la disponibilité, l’utilisation et les performances) <br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 1 et 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Non pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge<br>
Interconnexion Nvidia/NVLink : Non pris en charge

> [!IMPORTANT]
> Pour cette série de machines virtuelles, le quota de processeurs virtuels (cœurs) dans votre abonnement est initialement défini sur 0 dans chaque région. [Demandez une augmentation du quota de processeurs virtuels](../azure-portal/supportability/regional-quota-requests.md) pour cette série dans une [région disponible](https://azure.microsoft.com/regions/services/).
>
| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Débit du disque non mis en cache max. : IOPS/Mbits/s | Nombre max de cartes réseau |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20 000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40 000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80 000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80 000/800 | 8 |

1 GPU = une carte P100.

*Prenant en charge RDMA



## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

Pour tirer parti des fonctionnalités GPU de machines virtuelles de la série N Azure, installez des pilotes GPU NVIDIA.

[L’extension du pilote GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) installe les pilotes CUDA ou GRID NVIDIA appropriés sur une machine virtuelle de série N. Installez ou gérez l’extension à l’aide du portail Azure ou d’outils tels qu’Azure PowerShell ou les modèles Azure Resource Manager. Consultez la [documentation sur l’extension du pilote GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) pour connaître les systèmes d’exploitation pris en charge et les étapes de déploiement. Pour des informations générales sur les extensions de machine virtuelle, consultez [Extensions et fonctionnalités des machines virtuelles Azure](./extensions/overview.md).

Si vous choisissez d’installer manuellement les pilotes GPU NVIDIA, voir [Configuration des pilotes GPU de série N pour Windows](./windows/n-series-driver-setup.md) ou [Configuration des pilotes GPU de série N pour Linux](./linux/n-series-driver-setup.md) pour connaître les systèmes d’exploitation et pilotes pris en charge, ainsi que les étapes d’installation et de vérification.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Pour plus d’informations sur les types de disques, consultez [Quels sont les types de disque disponibles dans Azure ?](disks-types.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.