---
title: Surveiller Virtual WAN à l'aide d'Azure Monitor Insights
description: En savoir plus sur la surveillance d’Azure Virtual WAN à l’aide d’Azure Monitor Insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/22/2021
ms.author: cherylmc
ms.openlocfilehash: 73209f93e2b7fd96dc6ebb540d62624249db5be8
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539229"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Azure Monitor Insights pour Virtual WAN

[Azure Monitor Insights](../azure-monitor/insights/network-insights-overview.md) pour Azure Virtual WAN offre aux utilisateurs et aux opérateurs la possibilité d'afficher l'état et le statut d'un WAN virtuel, présenté via une carte topologique autodécouverte. Les superpositions de l'état et du statut des ressources sur la carte vous fournissent une vue instantanée de l'intégrité globale du WAN virtuel. Sur la carte, vous pouvez parcourir les ressources via un accès en un clic aux pages de configuration des ressources du portail Virtual WAN.

Les métriques disponibles au niveau des ressources Virtual WAN sont collectées et présentées via un classeur de métriques Virtual WAN pré-empaqueté. Le classeur présente les métriques disponibles aux niveaux du WAN virtuel, du hub, de la passerelle et de la connexion. Cet article vous guide tout au long des étapes à suivre pour utiliser Azure Monitor Insights pour Virtual WAN afin d’afficher la topologie et les métriques de votre WAN virtuel dans un seul emplacement.

> [!NOTE]
> L’option de menu **Insights** se trouve dans le portail Virtual WAN sous **Supervision**. Vous pouvez également accéder au Classeur de métriques et à la Topologie Virtual WAN à l’aide d’Azure Monitor pour réseaux. Pour plus d'informations, consultez [Azure Monitor pour réseaux](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Avant de commencer

Pour appliquer la procédure décrite dans cet article, vous devez disposer d'un WAN virtuel composée d'un ou plusieurs hubs. Pour créer un WAN virtuel et un hub, suivez les étapes décrites dans ces articles :

* [Créer un réseau étendu virtuel](virtual-wan-site-to-site-portal.md#openvwan)
* [Créer un hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Afficher la topologie du WAN virtuel

Accédez à **Portail Azure** > **Virtual WAN**. Dans le menu **Surveiller** du volet de gauche, sélectionnez **Insights (préversion)** . La vue **Insights** apparaît. Celle-ci présente la carte des dépendances Virtual WAN et un mini-classeur **Métriques** de haut niveau.

**Figure 1 : Menu Surveiller > Insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Capture d'écran représentant la vue Insights (préversion)." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

Dans la vue **Insights**, vous pouvez consulter les ressources Virtual WAN autodécouvertes. Ces ressources comprennent des hubs, des passerelles, des pare-feu, des connexions, des réseaux virtuels spoke, des appliances virtuelles réseau de tiers et des branches dans un réseau Virtual WAN de bout en bout. Pour obtenir un exemple, reportez-vous à la **Figure 2**.

L’état de la ressource et le statutsont codés par couleur et superposés aux icônes de ressource dans la carte. Les métriques Virtual WAN de haut niveau, comme les capacités des hubs et l'utilisation de la passerelle, apparaissent sur le côté droit de la fenêtre, dans un mini-classeur.

**Figure 2 : vue Insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Capture d'écran représentant la vue Insights." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Affichage des dépendances

La vue **Dépendances** de Virtual WAN vous permet de visualiser la vue interconnectée de toutes les ressources Virtual WAN, largement organisées dans une architecture hub-and-spoke.

**Figure 3 : affichage Dépendances du WAN virtuel**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Capture d'écran représentant la vue Dépendances." lightbox="./media/azure-monitor-insights/dependency-map.png":::

La carte de la vue **Dépendances** affiche les ressources suivantes sous forme de graphique connexe :

* Hubs de WAN virtuel dans les différentes régions Azure.
* Réseaux virtuels spoke connectés directement au hub.
* Sites de branche VPN et Azure ExpressRoute, utilisateurs P2S connectés à chaque hub via leurs connexions ExpressRoute, S2S et P2S respectives, et passerelles de réseau virtuel.
* Pare-feu Azure (y compris les proxys de pare-feu tiers) déployés dans un hub (hub sécurisé).
* Appliances virtuelles réseau de tiers qui sont déployées dans des réseaux virtuels spoke.

La carte des dépendances affiche également des réseaux virtuels connectés indirectement (réseaux virtuels qui sont appairés avec des réseaux virtuels spoke Virtual WAN).

La carte des dépendances facilite la navigation vers les paramètres de configuration de chaque ressource. Par exemple, vous pouvez pointer sur la ressource hub pour afficher la configuration basique de la ressource, comme la région et le préfixe de hub. Cliquez avec le bouton droit pour accéder à la page Portail Azure de la ressource hub.

**Figure 4 : accéder aux informations spécifiques à la ressource**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Capture d'écran montrant comment accéder aux informations spécifiques aux ressources.":::

La barre de recherche et de filtre de la vue **Dépendances** permet d'effectuer facilement des recherches dans le graphique. Différents filtres permettent de restreindre votre recherche à un chemin et à un état spécifiques.

**Figure 5 : recherche et filtrage**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Capture d'écran représentant la barre de recherche et de filtre." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Métriques détaillées

Vous pouvez sélectionner **Afficher les métriques détaillées** pour accéder à la page détaillée **Métriques**. La page **Métriques** est un tableau de bord préconfiguré avec des onglets distincts. Ces onglets fournissent des informations utiles sur la capacité, les performances et l'utilisation des ressources au niveau du WAN virtuel, du hub et des connexions individuelles.

**Figure 6 : tableau de bord Métriques détaillé**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Capture d'écran représentant le tableau de bord des métriques détaillées." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d'informations, consultez [Métriques dans Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).
* Pour obtenir une description complète de toutes les métriques de Virtual WAN, consultez [Surveillance de Virtual WAN](monitor-virtual-wan.md).
