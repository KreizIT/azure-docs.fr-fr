---
title: Créer des liste de ressources partagées avec le mappage de l’abonnement Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Indique comment créer des liste de ressources partagées pour votre cloud privé grâce au mappage de l’abonnement Azure
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2716849295b2a83d88cf3fc190545cceb2369
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283256"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Créez des liste de ressources partagées pour votre cloud privé grâce au mappage de l’abonnement Azure
Le mappage de l’abonnement Azure vous permet de créer des liste de ressources partagées pour votre Cloud privé à partir des liste de ressources partagées vSphere disponibles. Dans le portail CloudSimple, vous pouvez afficher et gérer l’abonnement Azure pour vos clouds privés.

> [!NOTE]
> Le mappage d’une liste de ressources partagées mappe également toutes les liste de ressources partagées enfants. Un pool de ressources parent ne peut pas être mappé si l’un de ses pools de ressources enfants est déjà mappé.

1. [Accédez au portail CloudSimple](access-cloudsimple-portal.md).
2. Ouvrez la page **Ressources** et sélectionnez **Mappage d’abonnements Azure**.  
3. Cliquez sur **Edit Azure subscription mapping** (Modifier le mappage des abonnements Azure).  
4. Pour mapper les pools de ressources disponibles, sélectionnez-les sur la gauche et cliquez sur la flèche pointant à droite. 
5. Pour supprimer des mappages, sélectionnez-les sur la droite et cliquez sur la flèche pointant à gauche. 

    ![Abonnements Azure](media/resources-azure-mapping.png)

6. Cliquez sur **OK**.
