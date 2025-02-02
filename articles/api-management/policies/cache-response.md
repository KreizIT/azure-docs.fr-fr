---
title: Exemple de la stratégie de gestion des API - Ajouter des fonctionnalités à un service back-end
titleSuffix: Azure API Management
description: Exemple de la stratégie de Gestion des API Azure - Montre comment ajouter des fonctionnalités à un service principal. Par exemple, accepter un nom d’emplacement au lieu des latitude et longitude dans une API de prévisions météorologiques.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: danlep
ms.openlocfilehash: 561912a534d36768416f7343a92cc02b61498826
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618777"
---
# <a name="add-capabilities-to-a-backend-service"></a>Ajouter des fonctionnalités à un service principal

Cet article présente un exemple de la stratégie de Gestion des API Azure qui montre comment ajouter des fonctionnalités à un service principal. Par exemple, accepter un nom d’emplacement au lieu des latitude et longitude dans une API de prévisions météorologiques. Pour définir ou modifier un code de stratégie, suivez les étapes décrites dans [Définir ou modifier une stratégie](../set-edit-policies.md). Pour voir d’autres exemples, consultez [Exemples de stratégie](../policy-reference.md).

## <a name="policy"></a>Policy

Collez le code dans le bloc **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les stratégies APIM :

+ [Stratégies de transformation](../api-management-transformation-policies.md)
+ [Exemples de stratégie](../policy-reference.md)