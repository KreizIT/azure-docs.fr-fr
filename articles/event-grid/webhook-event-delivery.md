---
title: Remise d’événement WebHook
description: Cet article décrit la remise des événements webhook et la validation des points de terminaison lors de l’utilisation de webhooks.
ms.topic: conceptual
ms.date: 10/13/2021
ms.openlocfilehash: 35b088f18b4261760d7908a8e779dbc1bda56501
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062991"
---
# <a name="webhook-event-delivery"></a>Remise d’événements webhook
Un Webhook constitue l’un des nombreux moyens de recevoir des événements provenant d’Azure Event Grid. Lorsqu'un nouvel événement est prêt, le service Event Grid envoie une requête HTTP (POST) au point de terminaison configuré avec l'événement dans le corps de la requête.

Comme de nombreux autres services qui prennent en charge les Webhooks, Event Grid vous demande de prouver que vous êtes propriétaire de votre point de terminaison Webhook avant de démarrer la diffusion d'événements vers ce point de terminaison. Cette condition empêche tout utilisateur malveillant d'inonder votre point de terminaison d'événements. Lorsque vous utilisez un des trois services Azure répertoriés ci-dessous, l'infrastructure Azure gère automatiquement cette validation :

- Azure Logic Apps avec [connecteur Event Grid](/connectors/azureeventgrid/)
- Azure Automation via [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions avec [déclencheur Event Grid](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Validation de point de terminaison avec des événements Event Grid

Si vous utilisez un autre type de point de terminaison, comme une fonction Azure basée sur un déclencheur HTTP, le code de votre point de terminaison doit participer à l'établissement d'une liaison de validation avec EventGrid. Event Grid prend en charge deux méthodes de validation de l'abonnement.

- **Établissement d'une liaison synchrone** : Au moment de la création de l'abonnement aux événements, Event Grid envoie un événement de validation d'abonnement à votre point de terminaison. Le schéma de cet événement est semblable à celui de n'importe quel autre événement Event Grid. La partie données de cet événement inclut une propriété `validationCode`. Votre application vérifie que la requête de validation concerne un abonnement à un événement attendu, et renvoie le code de validation dans la réponse de manière synchrone. Ce mécanisme d'établissement de liaison est pris en charge dans toutes les versions d'Event Grid.

- **Établissement d'une liaison asynchrone** : Dans certains cas, il est impossible de renvoyer le code de validation de manière synchrone. Par exemple, si vous utilisez un service tiers (comme [`Zapier`](https://zapier.com) ou [IFTTT](https://ifttt.com/)), vous ne pouvez pas envoyer le code de validation par programmation.

   À partir de la version 2018-05-01-preview, Event Grid prend en charge l'établissement d'une liaison de validation manuel. Si vous créez un abonnement aux événements à l'aide d'un kit de développement logiciel (SDK) ou d'un outil qui utilise l'API 2018-05-01-preview ou version ultérieure, Event Grid envoie une propriété `validationUrl` dans la partie données de l'événement de validation de l'abonnement. Pour terminer l'établissement de la liaison, recherchez cette URL dans les données d'événement et envoyez-lui une requête GET. Vous pouvez utiliser un client REST ou votre navigateur web.

   L'URL fournie est valable pendant **5 minutes**. Pendant ce temps, l’état d’approvisionnement de l’abonnement aux événements est `AwaitingManualAction`. Si vous n’effectuez pas la validation manuelle dans les 5 minutes, l’état d’approvisionnement est défini sur `Failed`. Vous devez recréer l’abonnement aux événements avant de commencer la validation manuelle.

   Ce mécanisme d’authentification requiert également que le point de terminaison webhook retourne un code d’état HTTP 200 afin qu’il sache que le POST pour l’événement de validation a été accepté avant qu’il ne puisse être placé dans le mode de validation manuelle. En d'autres termes, si le point de terminaison renvoie un code 200 mais ne renvoie pas de réponse de validation de manière synchrone, le mode est transmis au mode de validation manuelle. S’il y a une opération GET sur l’URL de validation dans les 5 minutes, l’établissement de liaison de validation est considéré comme réussi.

> [!NOTE]
> L’utilisation de certificats auto-signés pour la validation n’est pas prise en charge. Utilisez plutôt un certificat signé auprès d’une autorité de certification commerciale (AC).

### <a name="validation-details"></a>Détails de validation

- Au moment de la création/mise à jour de l'abonnement aux événements, Event Grid publie un événement de validation d'abonnement sur le point de terminaison cible.
- L’événement contient une valeur d’en-tête `aeg-event-type: SubscriptionValidation`.
- Le corps de l’événement dispose du même schéma que les autres événements Event Grid.
- La propriété `eventType` de l’événement est `Microsoft.EventGrid.SubscriptionValidationEvent`.
- La propriété `data` de l’événement inclut une propriété `validationCode` avec une chaîne générée de façon aléatoire. Par exemple : `validationCode: acb13…`.
- Les données d’événement incluent une propriété `validationUrl` avec une URL pour la validation manuelle de l’abonnement.
- Le tableau contient uniquement l’événement de validation. Les autres événements sont envoyés dans une requête distincte, une fois que vous avez renvoyé le code de validation.
- Les SDK de plan de données EventGrid ont des classes correspondant aux données des événements de validation d’abonnement et à la réponse de validation d’abonnement.

Un exemple de SubscriptionValidationEvent est illustré ci-dessous :

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/myeventsub/validate?id=0000000000-0000-0000-0000-00000000000000&t=2021-09-01T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2021-00-01T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Pour prouver que vous êtes propriétaire du point de terminaison, renvoyez le code de validation dans la propriété `validationResponse`, comme indiqué dans l’exemple suivant :

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Vous devez retourner un code d’état de réponse **HTTP 200 OK**. **HTTP 202 Accepté** n’est pas considéré comme une réponse de validation d’abonnement Event Grid valide. La requête HTTP doit s’achever dans un délai de 30 secondes. Si l’opération ne se termine pas dans les 30 secondes, elle est annulée et pourra faire l’objet d’une nouvelle tentative au bout de 5 secondes. Si toutes les tentatives échouent, elle est traitée comme une erreur d’établissement de liaison de validation.

Sinon, vous pouvez valider manuellement l’abonnement en envoyant une demande GET à l’URL de validation. L’abonnement aux événements reste dans un état d’attente jusqu’à ce qu’il soit validé. L’URL de validation utilise le port 553. Si les règles de pare-feu bloquent le port 553, il faudra peut-être les mettre à jour pour que l’établissement manuel d’une liaison réussisse.

Pour accéder à un exemple de gestion d'établissement de liaison de validation d'abonnement, consultez un [exemple C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validation de point de terminaison avec CloudEvents v1.0
CloudEvents v1.0 implémente sa propre [sémantique de protection contre les abus](webhook-event-delivery.md) en utilisant la méthode **HTTP OPTIONS**. Pour plus d'informations à ce sujet, cliquez [ici](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Lors de l’utilisation du schéma CloudEvents pour la sortie, Event Grid l’utilise avec la protection contre les abus CloudEvents v1.0 à la place du mécanisme d’événement de validation Event Grid.

## <a name="event-schema-compatibility"></a>Compatibilité du schéma d’événement
Quand une rubrique est créée, un schéma d’événement entrant est défini. Et quand un abonnement est créé, un schéma d’événement sortant est défini. Le tableau suivant vous montre la compatibilité autorisée lors de la création d’un abonnement. 

| Schéma d’événement entrant | Schéma d’événement sortant | Pris en charge |
| ---- | ---- | ---- |
| Schéma Event Grid | Schéma Event Grid | Yes |
| | Schéma d’événements cloud v1.0 | Yes |
| | Schéma d’entrée personnalisé | No |
| Schéma d’événements cloud v1.0 | Schéma Event Grid | No |
| | Schéma d’événements cloud v1.0 | Yes |
| | Schéma d’entrée personnalisé | No |
| Schéma d’entrée personnalisé | Schéma Event Grid | Yes |
| | Schéma d’événements cloud v1.0 | Yes |
| | Schéma d’entrée personnalisé | Oui |

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant pour savoir comment résoudre les problèmes de validation des abonnements aux événements : 

[Résoudre les problèmes de validation des abonnements aux événements](troubleshoot-subscription-validation.md)
