---
title: Copier de façon incrémentielle des données d’un magasin de données source vers un magasin de données de destination
description: Ces didacticiels vous montrent comment copier de façon incrémentielle des données d’un magasin de données source dans un magasin de données de destination. Dans le premier, les données sont copiées à partir d’une table.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 543acb129d23a0b74434535306aca801d2f5fdd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763630"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Charger de façon incrémentielle des données d’un magasin de données source vers un magasin de données de destination

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dans une solution d’intégration de données, le chargement incrémentielle (ou delta) de données après un chargement initial complet de données est un scénario largement utilisé. Les tutoriels de cette section vous montrent différentes méthodes de chargement des données de façon incrémentielle à l’aide d’Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Chargement de données delta à partir d’une base de données à l’aide d’un filigrane
Dans ce cas, vous définissez un filigrane dans votre base de données source. Un filigrane est une colonne incluant une clé d’incrémentation ou l’horodatage de la dernière mise à jour. La solution de chargement delta charge les données modifiées entre un ancien filigrane et un nouveau filigrane. Le flux de travail de cette approche est illustré dans le diagramme suivant : 

:::image type="content" source="media/tutorial-incremental-copy-overview/workflow-using-watermark.png" alt-text="Flux de travail d’utilisation d’un filigrane":::

Pour des instructions pas à pas, consultez les didacticiels suivants : 
- [Copier de façon incrémentielle les données d’une table d’Azure SQL Database dans un stockage Blob Azure](tutorial-incremental-copy-powershell.md)
- [Copier de façon incrémentielle des données provenant de plusieurs tables d’une instance SQL Server vers Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

Pour les modèles, consultez ce qui suit :
- [Copie delta avec une table de contrôles](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Chargement de données delta à partir d’une base de données SQL à l’aide de la technologie Change Tracking
La technologie Change Tracking est une solution légère dans SQL Server et Azure SQL Database qui fournit un mécanisme de suivi des modifications efficace pour les applications. Elle permet à une application d’identifier facilement les données qui ont été insérées, mises à jour ou supprimées. 

Le flux de travail de cette approche est illustré dans le diagramme suivant :

:::image type="content" source="media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png" alt-text="Flux de travail pour l’utilisation de Change Tracking":::

Pour des instructions pas à pas, consultez les didacticiels suivants : <br/>
- [Copier de façon incrémentielle des données d’Azure SQL Database dans le stockage Blob Azure à l’aide de la technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Chargement des fichiers nouveaux et modifiés uniquement à l’aide de LastModifiedDate
Pour copier les fichiers nouveaux et modifiés, vous devez utiliser LastModifiedDate dans le magasin de destination. ADF analyse tous les fichiers du magasin source, applique le filtre de fichier par date de dernière modification et copie uniquement le nouveau fichier mis à jour depuis son dernier emplacement dans le magasin de destination.  N’oubliez pas que si vous laissez ADF analyser d’énormes quantités de fichiers, mais que vous ne copiez que quelques fichiers vers la destination, cela peut prendre beaucoup de temps en raison du processus d’analyse de fichiers.   

Pour des instructions pas à pas, consultez les didacticiels suivants : <br/>
- [Copier de façon incrémentielle des fichiers nouveaux et modifiés uniquement en fonction de la valeur LastModifiedDate du Stockage Blob Azure vers le Stockage Blob Azure](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Pour les modèles, consultez ce qui suit :
- [Copier les nouveaux fichiers par LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Chargement de nouveaux fichiers uniquement à l’aide d’un nom de dossier ou de fichier partitionné chronologiquement.
Vous pouvez copier les nouveaux fichiers uniquement, où les fichiers ou dossiers ont déjà été partitionnés chronologiquement avec des informations de tranches de temps comme composants du nom de fichier ou de dossier (par exemple, /aaaa/mm/jj/fichier.csv). Il s’agit là de l’approche la plus performante pour le chargement incrémentiel de nouveaux fichiers. 

Pour des instructions pas à pas, consultez les didacticiels suivants : <br/>
- [Copier de façon incrémentielle de nouveaux fichiers uniquement en fonction d’un nom de dossier ou de fichier partitionné chronologiquement du Stockage Blob Azure vers le Stockage Blob Azure](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Étapes suivantes
Accéder au didacticiel suivant : 

> [!div class="nextstepaction"]
>[Copier de façon incrémentielle les données d’une table d’Azure SQL Database dans un stockage Blob Azure](tutorial-incremental-copy-powershell.md)
