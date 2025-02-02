---
title: Intégrer votre flux de données à Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Prise en main de l’intégration de vos flux de données à Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: mbullwin
ms.openlocfilehash: 403fd0b41d2fc9e0db56aaa4eadab3c27e5d1991
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341247"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Procédure : Intégration de vos données de métriques à Metrics Advisor

Utilisez cet article pour en savoir plus sur l’intégration de vos données à Metrics Advisor. 

## <a name="data-schema-requirements-and-configuration"></a>Configuration requise et configuration des schémas de données

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]
Si vous n’êtes pas sûr de certains termes, reportez-vous au [glossaire](../glossary.md).

## <a name="avoid-loading-partial-data"></a>Éviter de charger des données partielles

Les données partielles sont dues à des incohérences entre les données stockées dans Metrics Advisor et la source de données. Cela peut se produire lorsque la source de données est mise à jour une fois que les données ont été extraites par Metrics Advisor. Metrics Advisor n’extrait les données d’une source de données donnée qu’une seule fois.

Par exemple, si une mesure a été intégrée à Metrics Advisor pour la surveillance. Metrics Advisor récupère correctement les données de métriques à l’horodateur A et effectue la détection des anomalies sur ce dernier. Toutefois, si les données de métrique de cet horodatage A particulier ont été actualisées après l’ingestion des données. La nouvelle valeur de données ne sera pas récupérée.

Vous pouvez essayer de [renvoyer](manage-data-feeds.md#backfill-your-data-feed) les données d’historique (décrit ultérieurement) pour atténuer les incohérences, mais cela ne déclenche pas de nouvelles alertes d’anomalie, si des alertes pour ces points d’heure ont déjà été déclenchées. Ce processus peut ajouter une charge de travail supplémentaire au système et n’est pas automatique.

Pour éviter de charger des données partielles, nous vous recommandons deux approches :

* Générer des données dans une transaction :

    Vérifiez que les valeurs de mesures pour toutes les combinaisons de dimensions au même horodatage sont stockées dans la source de données en une seule transaction. Dans l’exemple ci-dessus, attendez que les données de toutes les sources de données soient prêtes, puis chargez-les dans Metrics Advisor en une seule transaction. Metrics Advisor peut interroger le flux de données régulièrement jusqu’à ce que les données soient récupérées (ou partiellement).

* Retardez l’ingestion des données en définissant une valeur appropriée pour le paramètre **décalage de l’heure d’ingestion** :

    Définissez le paramètre de **décalage de l’heure d’ingestion** pour votre flux de données afin de retarder l’ingestion jusqu’à ce que les données soient entièrement préparées. Cela peut être utile pour certaines sources de données qui ne prennent pas en charge les transactions telles que le service de table Azure. Pour plus d’informations, consultez les [paramètres avancés](manage-data-feeds.md#advanced-settings).

## <a name="start-by-adding-a-data-feed"></a>Commencer en ajoutant un flux de données

Après vous être connecté à votre portail Metrics Advisor et choisir votre espace de travail, cliquez sur la **prise en main**. Ensuite, sur la page principale de l’espace de travail, cliquez sur **ajouter un flux de données** dans le menu de gauche.

### <a name="add-connection-settings"></a>Ajouter des paramètres de connexion

#### <a name="1-basic-settings"></a>1. Paramètres de base
Vous allez ensuite entrer un ensemble de paramètres pour connecter votre source de données de série chronologique. 
* **Type de source** : Type de source de données dans laquelle les données de la série chronologique sont stockées.
* **Granularité** : Intervalle entre des points de données consécutifs dans vos données de série chronologique. Metrics Advisor prend en charge : Annuelle, mensuelle, hebdomadaire, quotidienne, horaire et personnalisée. L’intervalle le plus bas pris en charge par l’option de personnalisation est de 300 secondes.
  * **Secondes** : Nombre de secondes pendant lesquelles *granularityName* est défini sur *personnaliser le*.
* **Ingérer des données à partir de (UTC)**  : Heure de début de la ligne de base pour l’ingestion des données. `startOffsetInSeconds` est souvent utilisé pour ajouter un décalage pour aider à la cohérence des données.

#### <a name="2-specify-connection-string"></a>2. Spécifier une chaîne de connexion
Ensuite, vous devez spécifier les informations de connexion pour la source de données. Pour plus d’informations sur les autres champs et la connexion de différents types de sources de données, consultez [Procédure : Connecter différentes sources de données](../data-feeds-from-different-sources.md).

#### <a name="3-specify-query-for-a-single-timestamp"></a>3. Spécifier une requête pour un horodatage unique
<!-- Next, you'll need to specify a query to convert the data into the required schema, see [how to write a valid query](../tutorials/write-a-valid-query.md) for more information.  -->

Pour plus d’informations sur les différents types de sources de données, consultez [Procédure : Connecter différentes sources de données](../data-feeds-from-different-sources.md).

### <a name="load-data"></a>Charger les données

Après avoir entré la chaîne de connexion et la chaîne de requête, sélectionnez **charger les données**. Dans le cadre de cette opération, Metrics Advisor vérifie la connexion et l’autorisation de chargement des données, vérifie les paramètres nécessaires (@IntervalStart et @IntervalEnd) qui doivent être utilisés dans la requête et vérifie le nom de colonne de la source de données. 

En cas d’erreur à cette étape :
1. Vérifiez d’abord si la chaîne de connexion est valide. 
2. Ensuite, vérifiez si les autorisations sont suffisantes et si l’accès est accordé à l’adresse IP du worker d’ingestion.
3. Vérifiez ensuite si les paramètres requis (@IntervalStart et @IntervalEnd) sont utilisés dans votre requête. 


### <a name="schema-configuration"></a>Configuration du schéma

Une fois le schéma de données chargé, sélectionnez les champs appropriés.

Si l’horodateur d’un point de données est omis, Metrics Advisor utilise l’horodateur lorsque le point de données est ingéré à la place. Pour chaque flux de données, vous pouvez spécifier au plus une colonne comme horodateur. Si vous obtenez un message indiquant qu’une colonne ne peut pas être spécifiée comme horodateur, vérifiez votre requête ou votre source de données, et s’il existe plusieurs horodateurs dans le résultat de la requête, et pas uniquement dans les données d’aperçu. Lors de l’ingestion de données, Metrics Advisor ne peut utiliser qu’un seul bloc (par exemple, un jour, une heure, en fonction de la granularité) des données de série chronologique de la source donnée à chaque fois.

|Sélection  |Description  |Notes  |
|---------|---------|---------|
| **Nom complet** | Nom à afficher dans votre espace de travail au lieu du nom de la colonne d’origine. | Optionnel.|
|**Timestamp**     | Horodatage d’un point de données. Si l’horodateur d’un point de données est omis, Metrics Advisor utilise l’horodateur lorsque le point de données est ingéré à la place. Pour chaque flux de données, vous pouvez spécifier au plus une colonne comme horodateur.        | Optionnel. Doit être spécifié avec au plus une colonne. Si vous recevez une erreur **La colonne ne peut pas être spécifiée en tant qu’horodateur**, vérifiez si votre source de données ou votre requête comporte des horodateurs en double.      |
|**Unité** :     |  Valeurs numériques dans le flux de données. Pour chaque flux de données, vous pouvez spécifier plusieurs unités, mais au moins une colonne doit être sélectionnée en tant qu’unité.        | Doit être spécifié avec au moins une colonne.        |
|**Dimension**     | Valeurs catégorielles. Une combinaison de différentes valeurs identifie une série chronologique à une seule dimension, par exemple : pays, langue, locataire. Vous pouvez sélectionner zéro ou plusieurs colonnes en tant que dimensions. Remarque : Soyez prudent lorsque vous sélectionnez une colonne qui n’est pas une chaîne comme dimension. | Optionnel.        |
|**Ignorer**     | Suppression de la colonne sélectionnée.        | Optionnel. Pour que les sources de données prennent en charge l’utilisation d’une requête pour obtenir des données, il n’existe aucune option « Ignorer ».       |

Si vous souhaitez ignorer les colonnes, nous vous recommandons de mettre à jour votre requête ou votre source de données pour exclure ces colonnes. Vous pouvez également ignorer les colonnes à l’aide des options **Ignorer les colonnes** puis **Ignorer** sur les colonnes spécifiques. Si une colonne doit être une dimension et qu’elle est définie par erreur comme *ignorée*, Metrics Advisor peut ingérer des données partielles. Par exemple, supposons que les données de votre requête se comportent comme suit :

| ID de ligne | Timestamp | Country | Langue | Revenu |
| --- | --- | --- | --- | --- |
| 1 | 2019/01/10 | Chine | ZH-CN | 10000 |
| 2 | 2019/01/10 | Chine | EN-US | 1 000 |
| 3 | 2019/01/10 | US | ZH-CN | 12 000 |
| 4 | 2019/11/11 | US | EN-US | 23000 |
| ... | ...| ... | ... | ... |

Si *Pays* est une dimension et *Langue* est définie sur *Ignoré*, la première et la deuxième lignes auront les mêmes dimensions pour un horodatage. Metrics Advisor utilisera arbitrairement une valeur des deux lignes. Metrics Advisor ne regroupera pas les lignes dans ce cas.

Après avoir configuré le schéma, sélectionnez **Vérifier le schéma**. Dans le cadre de cette opération, Metrics Advisor effectue les vérifications suivantes :
- Indique si l’horodateur des données interrogées se trouve dans un seul intervalle. 
- Indique s’il existe des valeurs en double retournées pour la même combinaison de dimensions dans un intervalle de métrique.  

### <a name="automatic-roll-up-settings"></a>Paramètres de regroupement automatique

> [!IMPORTANT]
> Si vous souhaitez activer l’analyse de la cause racine et d’autres fonctionnalités de diagnostic, les **paramètres de cumul automatique** doivent être configurés. Une fois activé, les paramètres de cumul automatique ne peuvent pas être modifiés.

Metrics Advisor peut effectuer automatiquement une agrégation (par exemple, SUM, MAX, MIN) sur chaque dimension lors de la réception, puis crée une hiérarchie qui sera utilisée dans les analyses de cas racine et d’autres fonctionnalités de diagnostic. 

Considérez les scénarios suivants :

* *« Je n’ai pas besoin d’inclure l’analyse de cumul pour mes données. »*

    Vous n’avez pas besoin d’utiliser le correctif de Metrics Advisor.

* *« Mes données ont déjà été cumulées et la valeur de dimension est représentée par : NULL ou vide (valeur par défaut), NULL uniquement, Autres. »*

    Cette option signifie que Metrics Advisor n’a pas besoin de cumuler les données, car les lignes sont déjà additionnées. Par exemple, si vous sélectionnez *NULL uniquement*, la deuxième ligne de données de l’exemple ci-dessous sera considérée comme une agrégation de tous les pays et de la langue *en-US* ; la quatrième ligne de données qui a une valeur vide pour *Pays* est toutefois considérée comme une ligne ordinaire qui peut indiquer des données incomplètes.
    
    | Country | Langue | Revenu |
    |---------|----------|--------|
    | Chine   | ZH-CN    | 10000  |
    | (NULL)  | EN-US    | 999999 |
    | US      | EN-US    | 12 000  |
    |         | EN-US    | 5 000   |

* *« J’ai besoin de Metrics Advisor pour cumuler mes données en calculant Somme/Max/Min/Moyenne/Nombre et les représenter par {une chaîne}. »*

    Certaines sources de données telles que Cosmos DB ou le Stockage Blob Azure ne prennent pas en charge certains calculs tels que les *group by* ou *cube*. Metrics Advisor fournit l’option de cumul pour générer automatiquement un cube de données pendant l’ingestion.
    Cette option signifie que vous avez besoin de Metrics Advisor pour calculer le cumul à l’aide de l’algorithme que vous avez sélectionné et utiliser la chaîne spécifiée pour représenter le cumul dans Metrics Advisor. Cela ne modifie pas les données de votre source de données.
    Supposons, par exemple, que vous ayez un ensemble de séries chronologiques qui représente les mesures de ventes avec la dimension (pays, région). Pour un horodatage donné, il peut se présenter comme suit :


    | Country       | Région           | Ventes |
    |---------------|------------------|-------|
    | Canada        | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | États-Unis | Montana          | 100   |


    Après l’activation de la restauration automatique avec *Sum*, Metrics Advisor calcule les combinaisons de dimensions et additionne les métriques pendant l’ingestion des données. Le résultat peut être :

    | Country       | Région           | Ventes |
    | ------------ | --------------- | ---- |
    | Canada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | NULL          | British Columbia | 500   |
    | États-Unis | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Canada        | NULL             | 600   |
    | États-Unis | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` signifie que la somme des ventes au Canada (toutes les régions) est de 600.

    Voici la transformation dans le langage SQL.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Tenez compte des éléments suivants avant d’utiliser la fonctionnalité de cumul automatique :

    * Si vous souhaitez utiliser *SUM* pour agréger vos données, assurez-vous que vos mesures sont additives dans chaque dimension. Voici quelques exemples de métriques *non additives* :
      - Mesures basées sur des fractions. Cela comprend le ratio, le pourcentage, etc. Par exemple, vous ne devez pas ajouter le taux de chômage de chaque état pour calculer le taux de chômage de l’ensemble du pays.
      - Chevauchement dans la dimension. Par exemple, vous ne devez pas ajouter le nombre de personnes dans chaque sport pour calculer le nombre de personnes qui aiment les sports, parce qu’il y a un chevauchement entre eux, une personne peut s’intéresser à plusieurs sports.
    * Pour garantir l’intégrité de l’ensemble du système, la taille du cube est limitée. Actuellement, la limite est de 1 million. Si vos données dépassent cette limite, l’ingestion échoue pour cet horodatage.

## <a name="advanced-settings"></a>Paramètres avancés

Il existe plusieurs paramètres avancés pour permettre la réception de données de manière personnalisée, par exemple en spécifiant un décalage d’ingestion ou une concurrence. Pour plus d’informations, consultez la section [paramètres avancés](manage-data-feeds.md#advanced-settings) de l’article gestion du flux de données.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Spécifiez un nom pour le flux de données et vérifiez la progression de l’ingestion
 
Donnez un nom personnalisé au flux de données, qui s’affichera dans votre espace de travail. Cliquez ensuite sur **Envoyer**. Dans la page Détails du flux de données, vous pouvez utiliser la barre de progression de l’ingestion pour afficher les informations d’état.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Barre de progression de l’ingestion" lightbox="../media/datafeeds/ingestion-progress.png":::


Pour vérifier les détails de l’échec d’ingestion : 

1. Cliquez sur **Afficher les détails**.
2. Cliquez sur **État** puis choisissez **Échec** ou **Erreur**.
3. Placez le curseur sur une ingestion ayant échoué et affichez le message détaillé qui s’affiche.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Vérifier l’ingestion des échecs":::

Un état d’*échec* indique que l’ingestion de cette source de données sera retentée ultérieurement.
Un état d’*erreur* indique que Metrics Advisor ne peut pas réessayer pour la source de données. Pour recharger des données, vous devez déclencher un renvoi/rechargement manuel.

Vous pouvez également recharger la progression d’une ingestion en cliquant sur **progression de l’actualisation**. Une fois l’ingestion des données terminée, vous pouvez cliquer sur les métriques et vérifier les résultats de la détection d’anomalie.

## <a name="next-steps"></a>Étapes suivantes
- [Gérer vos flux de données](manage-data-feeds.md)
- [Configurations pour différentes sources de données](../data-feeds-from-different-sources.md)
- [Configurer des métriques et affiner la configuration de la détection](configure-metrics.md)
