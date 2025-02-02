---
title: Nouveautés du service QnA Maker
titleSuffix: Azure Cognitive Services
description: Cet article présente les nouveautés de QnA Maker.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 07/16/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 959a6d5c5ed4b606c5a5850264422b6e460eb8f5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020442"
---
# <a name="whats-new-in-qna-maker"></a>Nouveautés de QnA Maker

Découvrir les nouveautés du service. Ces éléments peuvent publier des notes, des vidéos, des billets de blog et tout autre type d’informations. Marquez cette page pour rester aux faits des nouveautés du service.

[!INCLUDE [Custom question answering](./includes/new-version.md)]

## <a name="release-notes"></a>Notes de publication

Découvrez les nouveautés de QnA Maker.

### <a name="may-2021"></a>Mai 2021

* QnA Maker managé a été réintroduit en tant que fonctionnalité de réponse aux questions personnalisées dans la [ressource Analyse de texte](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics).
* La réponse aux questions personnalisées prend en charge les documents non structurés.
* L’[API prédéfinie](how-to/using-prebuilt-api.md) a été introduite pour générer des réponses pour les requêtes utilisateur à partir du texte du document passé via l’API.

### <a name="november-2020"></a>Novembre 2020

* Nouvelle version de QnA Maker lancée en préversion publique gratuite. En savoir plus [ici](https://techcommunity.microsoft.com/t5/azure-ai/introducing-qna-maker-managed-now-in-public-preview/ba-p/1845575).

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-QnA-managed-Now-in-Public-Preview/player]
* Création de ressources simplifiée
* Prise en charge de bout en bout des régions
* Modèle de classement soumis au Deep Learning
* Compréhension de la lecture des machines pour obtenir des réponses précises
  
### <a name="july-2020"></a>Juillet 2020

* [Métadonnées : `OR` combinaison logique de plusieurs paires de métadonnées](how-to/query-knowledge-base-with-metadata.md#logical-or-using-strictfilterscompoundoperationtype-property)
* [Étapes](how-to/network-isolation.md) permettant de configurer des points de terminaison Recherche cognitive pour qu’ils soient privés, mais toujours accessibles à QnA Maker.
* Les ressources gratuites de Recherche cognitive sont supprimées après [90 jours d’inactivité](how-to/set-up-qnamaker-service-azure.md#inactivity-policy-for-free-search-resources).

### <a name="june-2020"></a>Juin 2020

* Mise à jour du tutoriel [Power Virtual Agent](tutorials/integrate-with-power-virtual-assistant-fallback-topic.md) pour rendre les étapes plus rapides et plus simples

### <a name="may-2020"></a>Mai 2020

* [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](concepts/role-based-access-control.md)
* [Modification de texte enrichi](how-to/edit-knowledge-base.md#rich-text-editing-for-answer) pour les réponses

### <a name="march-2020"></a>Mars 2020

* La sécurité TLS 1.2 est maintenant appliquée pour toutes les requêtes HTTP adressées à ce service. Pour plus d’informations, consultez [Sécurité Azure Cognitive Services](../cognitive-services-security.md).

### <a name="february-2020"></a>Février 2020

* [Package NPM](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) avec l’API GenerateAnswer

### <a name="november-2019"></a>Novembre 2019

* [Support cloud US Government](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers) pour QnA Maker
* Fonctionnalité [multitour](./how-to/multiturn-conversation.md) dans la version en disponibilité générale
* [Support pour les échanges](./how-to/chit-chat-knowledge-base.md#language-support) disponible dans les langues de niveau 1

### <a name="october-2019"></a>2 octobre 2019

* Définition explicite de la langue pour toutes les bases de connaissances du service QnA Maker.

### <a name="september-2019"></a>Septembre 2019

* Importer et exporter avec le format de fichier XLS.

### <a name="june-2019"></a>Juin 2019

* Amélioration du [modèle d’outil de classement](concepts/query-knowledge-base.md#ranker-process) pour le français, l’italien, l’allemand, l’espagnol et le portugais

### <a name="april-2019"></a>Avril 2019

* Prise en charge de l’extraction de contenu de site web
* Prise en charge des [documents SharePoint](how-to/add-sharepoint-datasources.md) à partir d’un accès authentifié

### <a name="march-2019"></a>Mars 2019

* L’[apprentissage actif](how-to/improve-knowledge-base.md) fournit des suggestions de nouvelles alternatives de question basées sur des questions d’utilisateur réelles
* Amélioration du modèle d’[outil de classement](concepts/query-knowledge-base.md#ranker-process) du traitement en langage naturel (NLP, Natural Language Processing) pour l’anglais

> [!div class="nextstepaction"]
> [Créer un service QnA Maker](how-to/set-up-qnamaker-service-azure.md)

## <a name="cognitive-service-updates"></a>Mises à jour du service cognitif

[Annonces de mise à jour Azure pour Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
