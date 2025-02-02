---
title: Importer une API SOAP à l’aide du portail Azure | Microsoft Docs
description: Découvrez comment importer une représentation XML standard d’une API SOAP, puis tester l’API dans les portails des développeurs et Azure.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: danlep
ms.openlocfilehash: effb8b3820359539045a25244ced9f0dddc83c86
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629689"
---
# <a name="import-soap-api"></a>Importer une API SOAP

Cet article explique comment importer une représentation XML standard d’une API SOAP. Il explique également comment tester l’API Gestion des API.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Importer une API SOAP
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

## <a name="prerequisites"></a>Prérequis

Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importer et publier une API back-end

1. À partir du portail Azure, accédez à votre service Gestion des API, puis sélectionnez **API** dans le menu.
2. Sélectionnez **WSDL** dans la liste **Ajouter une nouvelle API**.

    ![API SOAP](./media/import-soap-api/wsdl-api.png)
3. Dans **Spécification WSDL**, entrez l’URL correspondant à votre API SOAP.
4. La case d’option **Requête SOAP directe** est sélectionnée par défaut. Avec cette sélection, l’API va être exposée en tant que SOAP. Le consommateur doit utiliser des règles SOAP. Si vous souhaitez « convertir l’API pour REST », suivez les étapes de [Import a SOAP API and convert to REST](restify-soap-api.md) (Importer une API SOAP et la convertir pour REST).

    ![Capture d’écran montrant la boîte de dialogue Créer à partir de WSDL dans laquelle vous pouvez entrer une spécification WSDL.](./media/import-soap-api/pass-through.png)
5. Appuyez sur la touche de tabulation.

    Les champs suivants sont automatiquement remplis avec les informations de l’interface de programmation d’applications SOAP : Nom d’affichage, nom et description.
6. Ajoutez un suffixe d’URL d’API. Le suffixe est un nom qui identifie cette API spécifique dans cette instance Gestion des API. Il doit être unique dans cette instance Gestion des API.
7. Publiez l’API en l’associant à un produit. Dans ce cas, le produit « *Illimité* » est utilisé.  Si vous souhaitez que l’API soit publiée et mise à la disposition des développeurs, ajoutez-la à un produit. Vous pouvez effectuer cette opération durant la création de l’API ou ultérieurement.

    Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs dans le portail des développeurs. Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API de ce produit. Si vous avez créé l’instance Gestion des API, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.

    Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :

    * **Starter**
    * **Illimité**   
8. Entrez d’autres paramètres d’API. Vous pouvez définir les valeurs lors de la création, ou les configurer ultérieurement en accédant à l’onglet **Paramètres**. Les paramètres sont expliqués dans le tutoriel [Importer et publier votre première API](import-and-publish.md#import-and-publish-a-backend-api).
9. Sélectionnez **Create** (Créer).

### <a name="test-the-new-api-in-the-administrative-portal"></a>Tester la nouvelle API dans le portail d’administration

Les opérations peuvent être directement appelées depuis le portail d’administration, qui permet d’afficher et de tester les opérations d’une API.  

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Appuyez sur l’onglet **Test**.
3. Sélectionnez une opération.

    La page affiche des champs pour les paramètres de requête et des champs pour les en-têtes. L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance Gestion des API, la clé est renseignée automatiquement, car vous êtes déjà administrateur. 
1. Appuyez sur **Envoyer**.

    Le serveur principal répond avec **200 OK** et certaines données.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)