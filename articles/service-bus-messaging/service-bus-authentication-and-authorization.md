---
title: Configuration de l’authentification et de l’autorisation Service Bus Azure | Microsoft Docs
description: Authentifiez des applications dans Service Bus avec l’authentification Signature d’accès partagé (SAS).
ms.topic: article
ms.date: 09/15/2021
ms.openlocfilehash: 1b1cc20df861f657ed9fff4704862e9ee36965a2
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002071"
---
# <a name="service-bus-authentication-and-authorization"></a>Authentification et de autorisation Service Bus
Il existe deux façons d’authentifier et d’autoriser l’accès aux ressources Azure Service Bus : Azure Active Directory (Azure AD) et les signatures d’accès partagé (SAP). Cet article vous explique l’utilisation de ces deux types de mécanismes de sécurité. 

## <a name="azure-active-directory"></a>Azure Active Directory
L’intégration Azure AD pour les ressources Service Bus fournit un contrôle d’accès en fonction du rôle Azure (RBAC), qui permet un contrôle affiné de l’accès d’un client aux ressources. Vous pouvez utiliser Azure RBAC pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d’application. Le principal de sécurité est authentifié par Azure AD pour retourner un jeton OAuth 2.0. Le jeton peut être utilisé pour autoriser une requête d’accès à une ressource Service Bus (file d’attente, rubrique, etc.).

Pour plus d’informations sur l’authentification avec Azure AD, consultez les articles suivants :

- [Authentifier avec des identités managées](service-bus-managed-service-identity.md)
- [Authentifier à partir d’une application](authenticate-application.md)

> [!NOTE]
> L’[API REST Service Bus](/rest/api/servicebus/) prend en charge l’authentification OAuth avec Azure AD.

> [!IMPORTANT]
> L’autorisation des utilisateurs ou des applications avec un jeton OAuth 2.0 retourné par Azure AD assure une meilleure sécurité que les signatures d’accès partagé. De plus, elle offre une plus grande simplicité d’utilisation. Azure AD vous évite d’avoir à stocker les jetons dans votre code. Vous êtes ainsi moins exposé au risque de failles de sécurité. Nous vous recommandons d’utiliser Azure AD avec vos applications Azure Service Bus dans la mesure du possible. 

## <a name="shared-access-signature"></a>Signature d’accès partagé
[L’authentification SAP](service-bus-sas.md) vous permet d’accorder un accès utilisateur aux ressources Service Bus avec des droits spécifiques. L’authentification SAP dans Service Bus implique la configuration d’une clé de chiffrement avec les droits associés sur une ressource Service Bus. Les clients peuvent alors accéder à cette ressource en présentant un jeton SAS qui se compose de la ressource URI à laquelle accéder et une échéance signée avec la clé configurée.

Vous pouvez configurer des clés pour SAS dans un espace de noms Service Bus. La clé s’applique à toutes les entités de messagerie dans cet espace de noms. Vous pouvez également configurer des clés sur les rubriques et files d’attente Service Bus. SAP est également pris en charge par [Azure Relay](../azure-relay/relay-authentication-and-authorization.md).

Pour utiliser SAS, vous pouvez configurer une règle d’autorisation d’accès partagé sur un espace de noms, une file d’attente ou une rubrique. Cette règle se compose des éléments suivants :

* *KeyName* : identifie la règle.
* *PrimaryKey* : clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *SecondaryKey* : clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *Rights* : représente la collection des droits **Listen**, **Send** ou **Manage** accordés.

Les règles d’autorisation configurées au niveau de l’espace de noms peuvent accorder l’accès à toutes les entités dans un espace de noms pour les clients avec des jetons signés à l’aide de la clé correspondante. Vous pouvez configurer jusqu’à 12 règles d’autorisation de ce type sur un espace de noms, une file d’attente ou une rubrique Service Bus. Par défaut, une règle d’autorisation d’accès partagé avec tous les droits est configurée pour chaque espace de noms dès sa mise en service initiale.

Pour accéder à une entité, le client requiert un jeton SAP créé à l’aide d’une règle d'autorisation d’accès partagé spécifique. Le jeton SAS est généré à l’aide du code HMAC-SHA256 d’une chaîne de ressource qui se compose de l’URI de la ressource à laquelle vous souhaitez accéder et d’une échéance avec une clé de chiffrement associée à la règle d’autorisation.

La prise en charge de l’authentification SAS pour Service Bus est incluse dans le Kit de développement Azure .NET SDK versions 2.0 et ultérieures. SAP inclut la prise en charge d’une règle d’autorisation d’accès partagé. Toutes les API qui acceptent une chaîne de connexion en tant que paramètre incluent la prise en charge des chaînes de connexion des services SAS.


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’authentification avec Azure AD, consultez les articles suivants :

- [Authentification avec des identités managées](service-bus-managed-service-identity.md)
- [Authentification à partir d’une application](authenticate-application.md)

Pour plus d’informations sur l’authentification avec SAS, consultez les articles suivants :

- [Authentification avec SAS](service-bus-sas.md)
