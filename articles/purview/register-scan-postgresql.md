---
title: Se connecter à PostgreSQL et le gérer
description: Ce guide décrit comment se connecter à PostgreSQL dans Azure Purview, et utiliser les fonctionnalités de Purview pour analyser et gérer votre source PostgreSQL.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 4d87a78c809791f01745f4406b58832258ea4d36
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548536"
---
# <a name="connect-to-and-manage-postgresql-in-azure-purview-preview"></a>Se connecter à PostgreSQL et le gérer dans Azure Purview (préversion)

Cet article décrit comment inscrire PostgreSQL et comment s’authentifier et interagir avec PostgreSQL dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

> [!IMPORTANT]
> PostgreSQL en tant que source est actuellement en PRÉVERSION. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| Oui|

Les versions du serveur PostgreSQL prises en charge sont les versions 8.4 à 12.x.

Lors de l’analyse de la source PostgreSQL, Purview prend en charge les opérations suivantes :

- Extraction de métadonnées incluant le serveur PostgreSQL, les bases de données, les schémas, les tables, les affichages et les colonnes de table/affichage.
- Récupération de la traçabilité des relations de ressources entre les tables et les affichages.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

* L’utilisateur de PostgreSQL doit avoir un accès en lecture aux tables système pour pouvoir accéder aux métadonnées avancées.

## <a name="register"></a>S’inscrire

Cette section décrit comment inscrire PostgreSQL dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire une nouvelle source PostgreSQL dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview Studio dans [Purview Studio](https://web.purview.azure.com/resource/).
1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans Inscrire des sources, sélectionnez **PostgreSQL**. Sélectionnez **Continuer**.

Dans l’écran **Inscrire des sources (PostgreSQL)** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

1. Entrez le nom du **Serveur** pour vous connecter à une source PostgreSQL. Cela peut être :
    * Un nom d’hôte utilisé pour se connecter au serveur de base de données. Par exemple : `MyDatabaseServer.com`
    * Adresse IP. Par exemple : `192.169.1.2`
    * Sa chaîne de connexion JDBC complète. Par exemple :

        ```
        jdbc:postgresql://COMPUTER_NAME_OR_IP:PORT/DATABASE_NAME
        ```

1. Entrez le **Port** utilisé pour se connecter au serveur de base de données (5432 par défaut pour PostgreSQL).

1. Sélectionnez une collection ou créez-en une (facultatif)

1. Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-postgresql/register-sources.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser PostgreSQL afin d’identifier les ressources et classer vos données automatiquement. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

### <a name="authentication-for-a-scan"></a>Authentification pour une analyse

Le type d’authentification pris en charge pour une source PostgreSQL est l’**Authentification de base**.

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Dans le centre d’administration, sélectionnez Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour créer un runtime d’intégration auto-hébergé.

1. Accédez aux **Sources**.

1. Sélectionnez la source PostgreSQL inscrite.

1. Sélectionnez **+ Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré

    1. **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :
        * Sélectionnez **Authentification de base** quand vous créez des informations d’identification.
        * Indiquez le nom d’utilisateur utilisé pour se connecter au serveur de base de données dans le champ d’entrée Nom d’utilisateur.
        * Stockez le nom d’utilisateur utilisé pour se connecter au serveur de base de données dans la clé secrète.

    1. **Base de données** : spécifiez le nom de l’instance de base de données à importer.

    1. **Schéma** : répertorie le sous-ensemble de schémas à importer, exprimé sous la forme d’une liste séparée par des points-virgules. Par exemple : `schema1; schema2`. Tous les schémas utilisateur sont importés si cette liste est vide. Tous les schémas système (par exemple, SysAdmin) et les objets sont ignorés par défaut. Si la liste est vide, tous les schémas disponibles sont importés.
        
        Les modèles de nom de schéma acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %. Par exemple : `A%; %B; %C%; D`
        * commençant par A ou
        * se terminant par B ou
        * contenant C ou
        * égalant D

        L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

    1. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la source PostgreSQL à analyser.

        > [!Note]
        > En règle générale, prévoyez 1 Go de mémoire pour 1 000 tables

        :::image type="content" source="media/register-scan-postgresql/scan.png" alt-text="Analyser PostgreSQL" border="true":::

1. Sélectionnez **Continuer**.

1. Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
