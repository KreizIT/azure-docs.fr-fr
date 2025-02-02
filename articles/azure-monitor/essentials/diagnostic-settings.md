---
title: Création de paramètres de diagnostic pour envoyer des métriques et journaux de plateforme Azure Monitor vers différentes destinations
description: Envoyez les journaux et métriques de plateforme Azure Monitor vers les journaux d’activité Azure Monitor, le stockage Azure ou Azure Event Hubs à l’aide d’un paramètre de diagnostic.
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 6de7ed1acffd7f4c05d912f7f565f4b811ebd357
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402032"
---
# <a name="create-diagnostic-settings-to-send-azure-monitor-platform-logs-and-metrics-to-different-destinations"></a>Création de paramètres de diagnostic pour envoyer des journaux et métriques de plateforme Azure Monitor vers différentes destinations

Cet article donne des informations sur la création et la configuration de paramètres de diagnostic dans le but d’envoyer des métriques et journaux de plateforme Azure vers différentes destinations.

Les [métriques de plateforme](./metrics-supported.md) sont envoyées automatiquement à [Azure Monitor Metrics](./data-platform-metrics.md) par défaut et sans configuration.

Les [journaux de plateforme](./platform-logs-overview.md), notamment le journal d’activité Azure et les journaux de ressources, fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Le journal d’activité existe en tant que tel, mais il peut être acheminé vers d’autres emplacements.  Les journaux de ressources ne sont pas collectés tant qu’ils ne sont pas envoyés vers une destination.

Chaque ressource Azure requiert son propre paramètre de diagnostic, qui définit les critères suivants :

- Sources : le type de données de métriques et de journaux à envoyer aux destinations définies dans le paramètre. Les types disponibles varient selon le type de ressource.
- Destinations : une ou plusieurs destinations d’envoi.

Un seul paramètre de diagnostic ne peut pas définir plus d’une destination. Si vous souhaitez envoyer des données à plus d’un type de destination (par exemple, deux espaces de travail Log Analytics), créez plusieurs paramètres. Chaque ressource peut avoir jusqu’à 5 paramètres de diagnostic.

La vidéo suivante vous guide dans l’acheminement des journaux de plateforme avec les paramètres de diagnostic.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

## <a name="sources"></a>Sources

Voici les sources possibles.

### <a name="metrics"></a>Mesures

Le paramètre **AllMetrics** achemine les métriques de plateforme d’une ressource vers des destinations supplémentaires. Cette option n’est pas nécessairement présente pour tous les fournisseurs de ressources.  

### <a name="logs"></a>Journaux d’activité

En ce qui concerne les journaux, vous pouvez sélectionner les catégories de journaux que vous souhaitez acheminer individuellement ou choisir un groupe de catégories.

> [!NOTE]
> Les groupes de catégories ne s’appliquent pas aux métriques. Toutes les ressources ne possèdent pas de groupes de catégories disponibles.

Les **groupes de catégories** permettent de collecter dynamiquement des journaux de ressources en fonction de regroupements prédéfinis au lieu de sélectionner des catégories de journaux individuelles. Microsoft définit les regroupements de façon à aider à surveiller des cas d’usage spécifiques dans tous les services Azure. Les catégories du groupe peuvent être mises à jour au fur et à mesure que de nouveaux journaux sont déployés ou que les évaluations changent. Lorsque des catégories de journaux sont ajoutées à un groupe de catégories ou en sont supprimées, votre collecte de journaux est modifiée automatiquement sans que vous ayez à mettre à jour vos paramètres de diagnostic.

Le recours à des groupes de catégories présente les conséquences suivantes : 
- Vous ne pouvez plus sélectionner individuellement les journaux de ressources en fonction de types de catégorie individuels.
- Vous ne pouvez plus appliquer de paramètres de rétention aux journaux envoyés au Stockage Azure.

Il existe actuellement deux groupes de catégories :
- **Tous**  : tous les journaux de ressources offerts par la ressource.
- **Audit** : tous les journaux de ressources qui enregistrent les interactions des clients avec les données ou les paramètres du service.

## <a name="destinations"></a>Destinations
Il est possible d’envoyer des journaux et métriques de plateforme aux destinations indiquées dans le tableau suivant. 

| Destination | Description |
|:---|:---|
| [Espace de travail Log Analytics](../logs/design-logs-deployment.md) | Les métriques sont converties au format journal. Cette option peut ne pas être disponible pour tous les types de ressources. Les envoyer au magasin Azure Monitor Logs (qui peut faire l’objet d’une recherche avec l’Analytique des journaux) aide à les intégrer dans des requêtes, des alertes et des visualisations avec des données de journaux existantes.  
| [Compte Azure Storage](../../storage/blobs/index.yml) | L’archivage des journaux et des métriques dans un compte de stockage Azure est utile à des fins d’audit, d’analyse statique ou de sauvegarde. Par rapport aux journaux d’activité Azure Monitor et à l’espace de travail Log Analytics, le stockage Azure est moins onéreux et les journaux peuvent y être conservés indéfiniment.  | 
| [Hubs d'événements](../../event-hubs/index.yml) | Envoyer des journaux et des métriques à Event Hubs permet de transmettre en continu des données à des systèmes externes tels que des outils SIEM tiers et d’autres solutions d’Analytique des journaux.  |
| [Intégrations partenaires d’Azure Monitor](/azure/partner-solutions/overview/)| Intégrations spécialisées entre Azure Monitor et d’autres plateformes de surveillance non-Microsoft. Utiles lorsque vous vous servez déjà de l’un des partenaires.  |

## <a name="requirements-and-limitations"></a>Conditions requises et limitations :

### <a name="metrics-as-a-source"></a>Métriques comme source
L’exportation des métriques présente certaines limitations.

- **L’envoi de métriques multidimensionnelles par le biais des paramètres de diagnostic n’est pas pris en charge actuellement**. Les métriques comportant des dimensions sont exportées sous forme de métriques unidimensionnelles aplaties, agrégées sur toutes les valeurs de dimension. *Par exemple* : la métrique « IOReadBytes » sur un blockchain peut être explorée et représentée sur un graphique par niveau de nœud. Toutefois, lorsqu’elle est exportée par le biais de paramètres de diagnostic, elle présente tous les octets lus de tous les nœuds.
- **Toutes les métriques ne sont pas exportables avec les paramètres de diagnostic**. En raison de limitations internes, toutes les métriques ne sont pas exportables vers Azure Monitor Logs ni l’Analytique des journaux. Pour plus d’informations, consultez la colonne Exportable dans la [liste des métriques prises en charge](./metrics-supported.md).

Pour contourner ces limitations sur des métriques spécifiques, vous pouvez les extraire manuellement avec [l’API REST Métriques](/rest/api/monitor/metrics/list) et les importer dans Azure Monitor Logs avec [l’API Collecteur de données d’Azure Monitor](../logs/data-collector-api.md).

### <a name="activity-log-as-a-source"></a>Journal d’activité comme source

> [!IMPORTANT]
> Avant de créer un paramètre de diagnostic pour le journal d’activité, vous devez d’abord désactiver toute configuration héritée. Pour plus d’informations, consultez [Méthodes de collecte héritées](../essentials/activity-log.md#legacy-collection-methods).


### <a name="destination-limitations"></a>Limitations des destinations

Les destinations du paramètre de diagnostic doivent être créées avant les paramètres de diagnostic. Cette destination ne doit pas nécessairement se trouver dans le même abonnement que la ressource qui envoie des journaux, à condition que l’utilisateur qui configure le paramètre ait un accès Azure RBAC approprié aux deux abonnements. Grâce à l’utilisation d’Azure Lighthouse, il est également possible d’envoyer des paramètres de diagnostic vers l’espace de travail d’un autre locataire de Répertoire actif Azure. Le tableau suivant fournit des exigences uniques pour chaque destination, y compris des restrictions régionales.

| Destination | Spécifications |
|:---|:---|
| Espace de travail Log Analytics | Il n’est pas obligatoire que l’espace de travail réside dans la même région que la ressource supervisée.|
| Compte Azure Storage | N'utilisez pas un compte de stockage existant sur lequel sont stockées d’autres données sans monitoring pour pouvoir mieux contrôler l’accès aux données. Si vous archivez également des journaux de diagnostic et des journaux de ressources ensemble, vous pouvez choisir d’utiliser le même compte de stockage pour regrouper toutes vos données d’analyse au même emplacement.<br><br>Pour envoyer les données dans l'espace de stockage immuable, définissez la stratégie d'immuabilité du compte de stockage, comme décrit dans [Définir et gérer des stratégies d’immuabilité pour le stockage d'objets blob](../../storage/blobs/immutable-policy-configure-version-scope.md). Vous devez suivre toutes les étapes décrites dans cet article, et notamment l’activation des écritures sur les objets blob d'ajout protégés.<br><br>Le compte de stockage doit se trouver dans la même région que la ressource supervisée si la ressource est régionale.|
| Event Hubs | La stratégie d’accès partagé pour l’espace de noms définit les autorisations dont dispose le mécanisme de diffusion en continu. La diffusion en continu vers Event Hubs requiert des autorisations de gestion, d’envoi et d’écoute. Pour mettre à jour le paramètre de diagnostic afin d’inclure la diffusion en continu, vous devez disposer de l’autorisation ListKey sur la règle d’autorisation Event Hubs.<br><br>L’espace de noms Event Hub doit se trouver dans la même région que la ressource supervisée si la ressource est régionale. <br><br> Les paramètres de diagnostic ne peuvent pas accéder aux ressources Event Hubs lorsque les réseaux virtuels sont activés. Vous devez activer le paramètre *Autoriser les services Microsoft approuvés* à contourner ce pare-feu dans Event Hubs pour que le service Azure Monitor (paramètres de diagnostic) soit autorisé à accéder à vos ressources Event Hubs.|
| Intégrations des partenaires | Varie selon le partenaire.  Pour plus d’informations, consultez la documentation sur les [intégrations de partenaires Azure Monitor](/azure/partner-solutions/overview/).  

### <a name="azure-data-lake-storage-gen2-as-a-destination"></a>Azure Data Lake Storage Gen2 comme destination

> [!NOTE]
> Actuellement, les comptes ADLS Gen2 ne sont pas pris en charge en tant que destination pour les paramètres de diagnostic, même s’ils peuvent être répertoriés comme une option valide dans le portail Azure.

## <a name="create-in-azure-portal"></a>Créer dans le portail Azure

Vous pouvez configurer des paramètres de diagnostic sur le portail Azure à partir du menu Azure Monitor ou du menu de la ressource.

1. L’endroit où vous configurez les paramètres de diagnostic dans le portail Azure dépend de la ressource.

   - Pour une ressource unique, cliquez sur **Paramètres de diagnostic** sous **Analyse** dans le menu de la ressource.

        ![Capture d’écran de la section Surveillance d’un menu de ressources dans le portail Azure avec les Paramètres de diagnostic en surbrillance.](media/diagnostic-settings/menu-resource.png)

   - Pour une ou plusieurs ressources, dans le menu Azure Monitor, sous **Paramètres**, cliquez sur **Paramètres de diagnostic**, puis sur la ressource.

        ![Capture d’écran de la section Paramètres dans le menu d’Azure Monitor avec les Paramètres de diagnostic en surbrillance.](media/diagnostic-settings/menu-monitor.png)

   - Pour le journal d’activité, dans le menu **Azure Monitor**, cliquez sur **Journal d’activité**, puis sur **Paramètres de diagnostic**. Veillez à désactiver toute configuration héritée pour le journal d’activité. Pour plus d’informations, voir [Désactiver des paramètres existants](./activity-log.md#legacy-collection-methods).

        ![Capture d’écran du menu d’Azure Monitor avec le Journal d’activité sélectionné et les Paramètres de diagnostic en surbrillance dans la barre de menus du journal d’activité.](media/diagnostic-settings/menu-activity-log.png)

2. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur **Ajouter le paramètre de diagnostic**.

   ![Ajouter le paramètre de diagnostic - aucun paramètre existant](media/diagnostic-settings/add-setting.png)

   Si des paramètres sont définis pour la ressource, la liste des paramètres déjà configurés s’affiche. Cliquez sur **Ajouter un paramètre de diagnostic** pour ajouter un paramètre ou sur **Modifier le paramètre** pour modifier un paramètre existant. Chaque paramètre ne peut pas avoir plus d’un type de destination.

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/diagnostic-settings/edit-setting.png)

3. Si votre paramètre n’a pas de nom, nommez-le.

      :::image type="Add diagnostic setting" source="media/diagnostic-settings/setting-new-blank.png" alt-text="Ajout d’un nouveau paramètre de diagnostic.":::

4. **Journaux et métriques à acheminer** : pour les journaux, choisissez un groupe de catégories ou cochez la case de chaque catégorie de données que vous souhaitez envoyer aux destinations qui seront spécifiées ultérieurement. La liste des catégories varie pour chaque service Azure. Choisissez *allMetrics* si vous souhaitez également stocker les métriques dans Azure Monitor Logs. 

5. **Détails de la destination** : cochez la case pour chaque destination. Lorsque vous cochez chaque case, des options s’affichent pour vous permettre d’ajouter des informations supplémentaires.

      ![Envoyer à Log Analytics ou à Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** : entrez l’abonnement et l’espace de travail.  Si vous n’avez pas d’espace de travail, vous devez [en créer un avant de continuer](../logs/quick-create-workspace.md).

    1. **Event Hubs** : spécifiez les critères suivants :
       - Abonnement auquel appartient le hub d’événements.
       - Espace de noms du hub d’événements : si vous n’en possédez pas encore, vous devez [en créer un](../../event-hubs/event-hubs-create.md).
       - Nom d’un hub d’événements (facultatif) auquel toutes les données seront envoyées. Si vous ne spécifiez pas de nom, un hub d’événements est créé pour chaque catégorie de journal. Si vous envoyez plusieurs catégories, vous pouvez spécifier un nom pour limiter le nombre de hubs d’événements créés. Pour plus de détails, voir [Quotas et limites d’Azure Event Hubs](../../event-hubs/event-hubs-quotas.md).
       - Stratégie Event Hub (facultatif) : une stratégie définit les autorisations dont dispose le mécanisme de diffusion en continu. Pour plus d’informations, consultez [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Stockage** : choisissez l’abonnement, le compte de stockage et la stratégie de rétention.

        ![Envoyer à Stockage](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Envisagez de définir la stratégie de rétention sur 0, et utilisez la [Stratégie de cycle de vie de S](/azure/storage/blobs/lifecycle-management-policy-configure) ou supprimez vos données du stockage à l’aide d’une tâche planifiée. Ces stratégies sont susceptibles de fournir un comportement plus cohérent. 
        >
        > Tout d’abord, si vous utilisez le stockage pour l’archivage, vous souhaitez généralement que vos données soient préservées plus de 365 jours. Ensuite, si vous choisissez une stratégie de rétention supérieure à 0, la date d’expiration est jointe aux journaux au moment du stockage. Vous ne pouvez pas modifier la date de ces journaux une fois qu’ils sont stockés. Par exemple, si vous définissez la stratégie de rétention pour *WorkflowRuntime* sur 180 jours, puis que vous la définissez 24 heures plus tard sur 365 jours, les journaux stockés pendant ces 24 heures sont automatiquement supprimés après 180 jours, tandis que tous les journaux suivants de ce type seront automatiquement supprimés après 365 jours. La modification ultérieure de la stratégie de rétention ne permet pas de conserver les journaux des premières 24 heures pendant 365 jours.

     1. **Intégration de partenaires** : vous devez d’abord installer une intégration de partenaires dans votre abonnement. Les options de configuration varient selon le partenaire. Pour plus d’informations, consultez [Intégrations de partenaires d’Azure Monitor](/azure/partner-solutions/overview/). 
    
6. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource, et les journaux sont envoyés aux destinations spécifiées au fur et à mesure de la génération de nouvelles données d’événement. Un délai de 15 minutes peut s’écouler entre le moment où un événement est émis et celui où il [s’affiche dans un espace de travail Log Analytics](../logs/data-ingestion-time.md).

## <a name="create-using-powershell"></a>Créer à l’aide de PowerShell

Pour créer un paramètre de diagnostic avec [Azure PowerShell](../powershell-samples.md), utilisez la cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). Pour obtenir une description des paramètres de celle-ci, consultez sa documentation.

> [!IMPORTANT]
> Vous ne pouvez pas utiliser cette méthode pour le journal d’activité Azure. Utilisez plutôt la méthode [Créer un paramètre de diagnostic dans Azure Monitor à l’aide d’un modèle Resource Manager](./resource-manager-diagnostic-settings.md) pour créer un modèle Resource Manager et le déployer avec PowerShell.

Voici un exemple de cmdlet PowerShell permettant de créer un paramètre de diagnostic utilisant les trois destinations.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Créer à l’aide d’Azure CLI

Pour créer un paramètre de diagnostic avec [Azure CLI](/cli/azure/monitor), utilisez la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create). Pour une description des paramètres de cette commande, consultez sa documentation.

> [!IMPORTANT]
> Vous ne pouvez pas utiliser cette méthode pour le journal d’activité Azure. Utilisez plutôt la méthode [Créer un paramètre de diagnostic dans Azure Monitor à l’aide d’un modèle Resource Manager](./resource-manager-diagnostic-settings.md) pour créer un modèle Resource Manager et le déployer avec CLI.

Voici un exemple de commande Azure CLI pour créer un paramètre de diagnostic utilisant les trois destinations. La syntaxe est légèrement différente en fonction de votre client.

# <a name="cmd"></a>[CMD](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Créer à l’aide d’un modèle Resource Manager
Consultez [Exemples de modèle Resource Manager pour les paramètres de diagnostic dans Azure Monitor](./resource-manager-diagnostic-settings.md) pour créer ou mettre à jour des paramètres de diagnostic avec un modèle Resource Manager.

## <a name="create-using-rest-api"></a>Créer à l’aide de l’API REST
Pour créer ou mettre à jour des paramètres de diagnostic à l’aide de l’[API REST Azure Monitor](/rest/api/monitor/), voir [Paramètres de diagnostic](/rest/api/monitor/diagnosticsettings).

## <a name="create-at-scale-using-azure-policy"></a>Créer à grande échelle à l’aide d’Azure Policy

Sachant qu’un paramètre de diagnostic doit être créé pour chaque ressource Azure, vous pouvez utiliser Azure Policy pour qu’un paramètre de diagnostic soit créé automatiquement à chaque création de ressource. Chaque type de ressource Azure est constitué d’un ensemble unique de catégories qui doivent être listées dans le paramètre de diagnostic. De ce fait, chaque type de ressource a besoin d’une définition de stratégie distincte. Certains types de ressources comportent des définitions de stratégie intégrées que vous pouvez affecter sans modification. Pour les autres types de ressources, vous devez créer une définition personnalisée. 

Maintenant que les groupes de catégories de journaux de ressources ont été ajoutés, vous pouvez choisir des options mises à jour dynamiquement à mesure que les catégories de journaux changent.  Pour plus d’informations, consultez les [sources des paramètres de diagnostic](#sources) indiquées plus haut dans cet article. 

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Définitions de stratégie intégrées pour Azure Monitor
Il existe deux définitions de stratégie intégrées pour chaque type de ressource : l’une pour l’envoi à un espace de travail Log Analytics et l’autre pour l’envoi à un Event Hub. Si vous avez besoin d’un seul emplacement, affectez cette stratégie pour le type de ressource. Si vous avez besoin des deux, affectez les deux définitions de stratégie pour la ressource.

Par exemple, l’image suivante montre les définitions de stratégie de paramètres de diagnostic intégrées pour Azure Data Lake Analytics.

![Capture d’écran partielle de la page de définitions d’Azure Policy montrant deux définitions de stratégie de paramètres de diagnostic intégrées pour Data Lake Analytics.](media/diagnostic-settings/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Définitions de stratégie personnalisées
Pour les types de ressource qui n’ont pas de stratégie intégrée, vous devez créer une définition de stratégie personnalisée. Vous pouvez le faire manuellement sur le portail Azure en copiant une stratégie intégrée existante et en la modifiant pour votre type de ressource. Toutefois, il est plus efficace de créer la stratégie programmatiquement en utilisant un script dans PowerShell Gallery.

Le script [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) crée des fichiers de stratégie pour un type de ressource déterminé que vous pouvez installer via PowerShell ou l’interface Azure CLI. Pour créer une définition de stratégie personnalisée pour des paramètres de diagnostic, procédez comme suit :

1. Vérifiez qu’[Azure PowerShell](/powershell/azure/install-az-ps) est installé.
2. Installez le script en utilisant la commande suivante :
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Exécutez le script en spécifiant la destination des journaux à l’aide des paramètres. Vous êtes ensuite invité à spécifier un abonnement et un type de ressource. 

   Par exemple, pour créer une définition de stratégie qui envoie des journaux vers un espace de travail Log Analytics et un Event Hub, utilisez la commande suivante :

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

   Vous pouvez aussi spécifier un abonnement et un type de ressource dans la commande. Par exemple, pour créer une définition de stratégie qui envoie des journaux vers un espace de travail Log Analytics et un Event Hub pour des bases de données Azure SQL Server, utilisez la commande suivante :

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. Le script crée des dossiers distincts pour chaque définition de stratégie. Chaque dossier contient trois fichiers nommés *azurepolicy.json*, *azurepolicy.rules.json* et *azurepolicy.parameters.json*. Si vous voulez créer la stratégie manuellement sur le portail Azure, vous pouvez copier et coller le contenu du fichier *azurepolicy.json*, car il contient la définition de stratégie complète. Utilisez les deux autres fichiers avec PowerShell ou l’interface Azure CLI pour créer la définition de stratégie à partir d’une ligne de commande.

   Les exemples suivants montrent comment installer la définition de stratégie à partir de PowerShell et de l’interface Azure CLI. Chaque exemple comprend des métadonnées pour spécifier une catégorie de **Supervision** pour regrouper la nouvelle définition de stratégie avec les définitions de stratégie intégrées.

   ```azurepowershell
   New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
   ```

   ```azurecli
   az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
   ```

### <a name="initiative"></a>Initiative
Au lieu de créer une affectation pour chaque définition de stratégie, une stratégie courante consiste à créer une initiative incluant les définitions de stratégie pour créer des paramètres de diagnostic pour chaque service Azure. Créez une affectation entre l’initiative et un groupe d’administration, un abonnement ou un groupe de ressources, en fonction de la façon dont vous gérez votre environnement. Cette stratégie offre les avantages suivants :

- Créez une seule affectation pour l’initiative au lieu de plusieurs pour chaque type de ressource. Utilisez la même initiative pour plusieurs groupes de supervision, abonnements ou groupes de ressources.
- Modifiez l’initiative quand vous devez ajouter un nouveau type de ressource ou une nouvelle destination. Par exemple, vos exigences initiales peuvent être d’envoyer les données uniquement vers un espace de travail Log Analytics et d’ajouter par la suite un Event Hub. Modifiez l’initiative au lieu de créer de nouvelles affectations.

Pour plus d’informations sur la création d’une initiative, consultez [Créer et attribuer une définition d’initiative](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition). Tenez compte des recommandations suivantes :

- Définissez la **Catégorie** sur **Supervision** pour la regrouper avec les définitions de stratégie intégrées et personnalisées associées.
- Au lieu de spécifier les détails de l’espace de travail Log Analytics et de l’Event Hub pour la définition de stratégie incluse dans l’initiative, utilisez un paramètre d’initiative commun. Ce paramètre vous permet de spécifier facilement une valeur commune pour l’ensemble des définitions de stratégie et de changer cette valeur si nécessaire.

![Capture d’écran montrant les paramètres de définition d’initiative.](media/diagnostic-settings/initiative-definition.png)

### <a name="assignment"></a>Affectation 
Affectez l’initiative à un groupe d’administration, un abonnement ou un groupe de ressources Azure en fonction de l’étendue de vos ressources à superviser. Un [groupe d’administration ](../../governance/management-groups/overview.md) est utile pour définir l’étendue de la stratégie, surtout si votre organisation possède plusieurs abonnements.

![Capture d’écran des paramètres de l’onglet De base dans la section Affecter une initiative des paramètres de diagnostic pour l’espace de travail Log Analytics dans le portail Azure.](media/diagnostic-settings/initiative-assignment.png)

Les paramètres initiative vous permettent de spécifier l’espace de travail ou d’autres détails une seule fois pour toutes les définitions de stratégie de l’initiative. 

![Capture d’écran montrant les paramètres d’initiative sous l’onglet Paramètres.](media/diagnostic-settings/initiative-parameters.png)

### <a name="remediation"></a>Correction
L’initiative s’applique à chaque machine virtuelle à mesure qu’elle est créée. Une [tâche de correction](../../governance/policy/how-to/remediate-resources.md) déploie les définitions de stratégie de l’initiative dans les ressources existantes, ce qui vous permet de créer des paramètres de diagnostic pour toutes les ressources qui ont déjà été créées.

Quand vous créez l’affectation à partir du portail Azure, vous avez la possibilité de créer une tâche de correction simultanément. Pour plus d’informations sur la correction, consultez [Corriger les ressources non conformes avec Azure Policy](../../governance/policy/how-to/remediate-resources.md).

![Capture d’écran montrant la correction d’initiative pour un espace de travail Log Analytics.](media/diagnostic-settings/initiative-remediation.png)

## <a name="troubleshooting"></a>Dépannage

### <a name="metric-category-is-not-supported"></a>La catégorie de métrique n’est pas prise en charge

Lors du déploiement d’un paramètre de diagnostic, vous recevez un message d’erreur semblable à *La catégorie de métrique « xxxx » n’est pas prise en charge*. Vous pouvez recevoir cette erreur même si un précédent déploiement a réussi. 

Le problème se produit avec un modèle Resource Manager, l’API REST des paramètres de diagnostic, Azure CLI et Azure PowerShell. Les paramètres de diagnostic créés via le portail Azure ne sont pas affectés, car seuls les noms des catégories prises en charge sont présentés.

Le problème est dû à une modification récente de l’API sous-jacente. Les catégories de métriques autres que « AllMetrics » ne sont pas prises en charge et ne l’ont jamais été, à l’exception de quelques services Azure spécifiques. Par le passé, les autres noms de catégorie étaient ignorés lors du déploiement d’un paramètre de diagnostic. Le backend Azure Monitor redirigeait ces catégories vers « AllMetrics ».  À compter de février 2021, le back-end a été mis à jour et vérifie maintenant spécifiquement que la catégorie de métriques fournie est exacte. Ce changement a entraîné l’échec de certains déploiements.

Si vous recevez cette erreur, mettez à jour vos déploiements en y remplaçant tous les noms de catégories de métriques par « AllMetrics » pour résoudre le problème. Si le déploiement cumulait déjà plusieurs catégories, une seule catégorie avec la référence « AllMetrics » doit être conservée. Si le problème persiste, contactez le support Azure sur le Portail Azure. 

### <a name="setting-disappears-due-to-non-ascii-characters-in-resourceid"></a>Le paramètre disparaît en raison de la présence de caractères non ASCII dans resourceID

Les paramètres de diagnostic ne prennent pas en charge les resourceID comportant des caractères non ASCII (par exemple Preproducción). Or, il n’est pas possible de renommer des ressources dans Azure. La seule option consiste donc à créer une nouvelle ressource sans caractères non ASCII. Si les caractères se trouvent dans le nom d’un groupe de ressources, vous pouvez déplacer les ressources dans un nouveau groupe. Sinon, vous devez recréer la ressource.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les journaux de la plateforme Azure](./platform-logs-overview.md)
