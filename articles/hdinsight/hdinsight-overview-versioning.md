---
title: Initiation au contrôle de version – Azure HDInsight
description: Découvrez le fonctionnement du contrôle de version dans Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: b7b23a1d7e549d5e1e5b712d2290722158d49f38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531382"
---
# <a name="how-versioning-works-in-hdinsight"></a>Fonctionnement du contrôle de version dans Azure HDInsight

Le service HDInsight comporte deux composants principaux : un fournisseur de ressources et des composants Apache Hadoop déployés sur un cluster. 

## <a name="hdinsight-resource-provider"></a>Fournisseur de ressources HDInsight

Dans Azure, les ressources sont mises à disposition par un fournisseur de ressources. Le fournisseur de ressources HDInsight est responsable de la création, de la gestion et de la suppression des clusters.

## <a name="hdinsight-images"></a>Images HDInsight

HDInsight utilise des images pour regrouper les composants de logiciels open source (OSS) qui peuvent être déployés sur un cluster. Ces images contiennent le système d’exploitation Ubuntu de base et les composants principaux, notamment Spark, Hadoop, Kafka, HBase et Hive.

## <a name="versioning-in-hdinsight"></a>Contrôle de version dans HDInsight

Microsoft met régulièrement à niveau les images et le fournisseur de ressources HDInsight pour inclure de nouvelles améliorations et fonctionnalités.

Une nouvelle version de HDInsight peut être créée lorsqu’une ou plusieurs des conditions suivantes sont remplies :

- Modifications ou mises à jour majeures apportées aux fonctionnalités du fournisseur de ressources HDInsight
- Versions majeures des composants OSS
- Modifications majeures apportées au système d’exploitation Ubuntu

Une nouvelle version d’image est créée lorsqu’une ou plusieurs des conditions suivantes sont remplies :

- Versions et mises à jour majeures et mineures des composants OSS
- Patchs et correctifs apportés à un composant de l’image

## <a name="next-steps"></a>Étapes suivantes

- [Composants et versions d’Apache dans HDInsight](./hdinsight-component-versioning.md)
