---
title: Qu’est-ce qu’une zone Azure DNS privée ?
description: Vue d’ensemble d’une zone DNS privée
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 3a8ec174cb1be02389487c442b4e1b3ff35dbf27
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532589"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Qu’est-ce qu’une zone Azure DNS privée ?

Le DNS privé Azure fournit un service DNS fiable et sécurisé pour gérer et résoudre les noms de domaine dans un réseau virtuel sans nécessiter l’ajout de solution DNS personnalisée. Grâce aux zones DNS privées, vous pouvez utiliser vos propres noms de domaine personnalisés au lieu des noms fournis par Azure actuellement disponibles. 

Les enregistrements contenus dans une zone DNS privée ne peuvent pas être résolus à partir d’Internet. La résolution des enregistrements DNS sur une zone DNS privée fonctionne uniquement à partir de réseaux virtuels qui sont liés à cette zone.

Vous pouvez lier une zone DNS privée à un ou plusieurs réseaux virtuels en créant des [liaisons de réseau virtuel](./private-dns-virtual-network-links.md).
Vous pouvez également activer la fonctionnalité d’[inscription automatique](./private-dns-autoregistration.md) pour gérer automatiquement le cycle de vie des enregistrements DNS des machines virtuelles qui sont déployées dans un réseau virtuel.

## <a name="limits"></a>limites

Pour savoir combien de zones DNS privées vous pouvez créer dans un abonnement et combien de jeux d’enregistrements peuvent être pris en charge dans une zone DNS privée, consultez [Limites d’Azure DNS](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="restrictions"></a>Restrictions

* Les zones DNS privées à étiquette unique ne sont pas prises en charge. Une zone DNS privée doit avoir au moins deux étiquettes. Par exemple, contoso.com a deux étiquettes séparées par un point. Une zone DNS privée peut avoir au maximum 34 étiquettes.
* Vous ne pouvez pas créer de délégations de zone (enregistrements NS) dans une zone DNS privée. Si vous envisagez d’utiliser un domaine enfant, vous pouvez directement créer le domaine en tant que zone DNS privée. Ensuite, vous pouvez le lier au réseau virtuel sans configurer de délégation de serveur de noms à partir de la zone parent.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer une zone privée dans Azure DNS à l’aide [d’Azure PowerShell](./private-dns-getstarted-powershell.md) ou [d’Azure CLI](./private-dns-getstarted-cli.md).

* Passez en revue certains [scénarios de zones privées](./private-dns-scenarios.md) courants qui peuvent être réalisés avec des zones privées dans Azure DNS.

* Pour les questions et réponses courantes sur les zones privées dans Azure DNS, consultez la [FAQ sur le DNS privé](./dns-faq-private.yml).
