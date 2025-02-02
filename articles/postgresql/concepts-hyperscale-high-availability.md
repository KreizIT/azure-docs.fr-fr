---
title: Haute disponibilité – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Concepts de haute disponibilité et de reprise d’activité après sinistre
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/15/2021
ms.openlocfilehash: d708bb832673d424b1737ad0bea00716f1f3f278
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524851"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Haute disponibilité dans Azure Database pour PostgreSQL - Hyperscale (Citus)

La haute disponibilité évite les temps d’arrêt des bases de données en conservant des réplicas de secours de chaque nœud dans un groupe de serveurs. Si un nœud devient inactif, Hyperscale (Citus) bascule les connexions entrantes du nœud défaillant vers son nœud de secours. Le basculement se produit en quelques minutes, et les nœuds promus disposent toujours de données actualisées par le biais de la réplication en streaming synchrone PostgreSQL.

Même si la haute disponibilité est activée, chaque nœud hyperscale (Citus) a son propre stockage localement redondant (LRS) avec trois réplicas synchrones gérés par le service stockage Azure.  En cas d’échec d’un seul réplica, il est détecté par stockage Azure service et est recréé de manière transparente. Pour la durabilité du stockage LRS, consultez les mesures [sur cette page](../storage/common/storage-redundancy.md#summary-of-redundancy-options).

Quand la haute disponibilité *est* activée, Hyperscale (CITUS) exécute un nœud de secours pour chaque nœud principal du groupe de serveurs. Le réplica principal et le serveur de secours utilisent la réplication PostgreSQL synchrone. Cette réplication permet aux clients d’avoir des temps d’arrêt prévisibles en cas de défaillance d’un nœud principal. En résumé, notre service détecte une défaillance sur les nœuds principaux et bascule sur les nœuds de secours sans aucune perte de données.

Pour tirer parti de la haute disponibilité sur le nœud coordinateur, les applications de base de données doivent détecter et retenter les connexions abandonnées et les transactions ayant échoué. Le coordinateur nouvellement promu sera accessible avec la même chaîne de connexion.

La reprise peut être divisée en trois étapes : la détection, le basculement et la reprise complète.  Hyperscale (Citus) exécute des contrôles d’intégrité réguliers sur chaque nœud. Il considère qu’un nœud est inactif au bout de quatre échecs. Il promeut alors un nœud de secours à l’état de nœud principal (basculement), puis provisionne un nouveau nœud de secours.
La réplication en streaming commence, mettant à jour le nouveau nœud.  Une fois toutes les données répliquées, le nœud a atteint la reprise complète.

### <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [activer la haute disponibilité](howto-hyperscale-high-availability.md) dans un groupe de serveurs Hyperscale (Citus).
