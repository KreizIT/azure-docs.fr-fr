---
title: Comment résoudre des problèmes de Container Insights | Microsoft Docs
description: Cet article explique comment résoudre des problèmes rencontrés avec Containers insights.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 04fea3c36cbff4e2c8ecb315f6e3f93bc92aa2b3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003247"
---
# <a name="troubleshooting-container-insights"></a>Résolution de problèmes liés à Container Insights

Lorsque vous configurez la surveillance de votre cluster Azure Kubernetes Service (AKS) avec Container insights, vous pouvez rencontrer un problème empêchant la collecte de données ou le statut de génération d’état. Cet article décrit en détail certains problèmes courants et indique la façon de les résoudre.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erreur d’autorisation durant une opération d’intégration ou de mise à jour

Lors de l’activation de Container insights ou de la mise à jour d’un cluster pour la prise en charge de la collecte des métriques, vous pouvez recevoir une erreur semblable à la suivante : *le client <identité de l’utilisateur> avec l’ID de l’objet <ID d’objet de l’utilisateur> n’est pas autorisé à effectuer l’action « Microsoft.Authorization/roleAssignments/write » sur l’étendue*.

Lors du processus de mise à jour ou d’intégration, une tentative d’attribution de rôle **Publication des métriques de surveillance** sur la ressource de cluster est effectuée. L’utilisateur qui initialise le processus d’activation de Container insights ou de mise à jour pour la prise en charge de la collecte des métriques doit disposer de l’autorisation **Microsoft.Authorization/roleAssignments/write** sur l’étendue de la ressource de cluster AKS. Seuls les membres des rôles intégrés **Propriétaire** et **Administrateur des accès utilisateur** se voient accorder l’accès. Si vos stratégies de sécurité nécessitent l’attribution d’autorisations au niveau granulaire, nous vous recommandons d’afficher [des rôles personnalisés](../../role-based-access-control/custom-roles.md) et de les attribuer aux utilisateurs qui en ont besoin.

Vous pouvez également accorder manuellement ce rôle à partir du portail Azure en effectuant les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le portail Azure, cliquez sur **Tous les services** en haut à gauche. Dans la liste des ressources, tapez **Kubernetes**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Azure Kubernetes**.
3. Dans la liste des clusters Kubernetes, sélectionnez-en un.
2. Cliquez sur **Contrôle d’accès (IAM)** dans le menu de gauche.
3. Sélectionnez **+ Ajouter** pour ajouter une attribution de rôle et sélectionnez le rôle **Publication des métriques de surveillance**. Sous le champ **Sélectionner**, tapez **AKS** afin de filtrer les résultats sur les principaux du service des clusters définis dans l’abonnement. Sélectionnez dans la liste celui qui est spécifique à ce cluster.
4. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Container Insights est activé, mais ne rapporte aucune information

Si Container insights est correctement configuré et activé, mais que les informations d’état ne s’affichent pas ou qu’aucun résultat n’est retourné suite à une requête de journal, vous pouvez diagnostiquer le problème de la façon suivante :

1. Vérifiez l’état de l’agent en exécutant la commande :

    `kubectl get ds omsagent --namespace=kube-system`

    La sortie doit ressembler à l’exemple suivant, qui indique que l’agent a été correctement déployé :

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Si vous avez des nœuds Windows Server, vérifiez l’état de l’agent en exécutant la commande suivante :

    `kubectl get ds omsagent-win --namespace=kube-system`

    La sortie doit ressembler à l’exemple suivant, qui indique que l’agent a été correctement déployé :

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Vérifiez l’état du déploiement avec la version de l’agent *06072018* (ou ultérieure) à l’aide de cette commande :

    `kubectl get deployment omsagent-rs -n=kube-system`

    La sortie doit ressembler à l’exemple suivant, qui indique que l’agent a été correctement déployé :

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Vérifiez l’état du pod pour confirmer qu’il est bien en cours d’exécution à l’aide de la commande : `kubectl get pods --namespace=kube-system`

    La sortie doit ressembler à l’exemple suivant, avec l’état *En cours d’exécution* pour l’agent OMS :

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Messages d’erreur

Le tableau ci-dessous récapitule les erreurs connues que vous pouvez rencontrer lors de l’utilisation de Container insights.

| Messages d’erreur  | Action |
| ---- | --- |
| Message d’erreur `No data for selected filters`  | L’établissement de la supervision du flux de données pour les nouveaux clusters peut prendre un certain temps. Patientez au moins 10 à 15 minutes avant l’affichage des données de votre cluster. |
| Message d’erreur `Error retrieving data` | Bien que le cluster Azure Kubernetes Service soit configuré pour la supervision de l’intégrité et des performances, une connexion est établie entre le cluster et l’espace de travail Azure Log Analytics. Un espace de travail Log Analytics est utilisé pour stocker toutes les données de supervision de votre cluster. Cette erreur peut se produire lorsque votre espace de travail Log Analytics a été supprimé. Vérifiez si l’espace de travail a été supprimé. Si c’est le cas, vous devrez réactiver la surveillance de votre cluster avec Container insights et spécifier un espace de travail existant ou créer un nouveau. Pour réactiver, vous devez [désactiver](container-insights-optout.md) la surveillance du cluster, puis [réactiver](container-insights-enable-new-cluster.md) Container insights. |
| `Error retrieving data` après l’ajout de Container insights à l’aide de la commande az aks cli | Lorsque vous activez la surveillance à l’aide de `az aks cli`, Container Insights peut ne pas être correctement déployé. Vérifiez si la solution est déployée. Pour vérifier cela, accédez à votre espace de travail Log Analytics et voyez si la solution est disponible en sélectionnant **Solutions** dans le volet de gauche. Pour résoudre ce problème, vous devez redéployer la solution en suivant les instructions suivantes de [déploiement de Container insights](container-insights-onboard.md). |

Pour vous aider à diagnostiquer le problème, nous vous fournissons un [script de résolution des problèmes](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot).

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Les pods ReplicaSet d’agent Container insights ne sont pas planifiés sur un cluster Kubernetes non Azure.

Les pods ReplicaSet d’agent Container insights dépendent des sélecteurs de nœuds suivants sur les nœuds Worker (ou agent) pour la planification :

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Si aucune étiquette de nœud n’est associée à vos nœuds Worker, les pods ReplicaSet d’agent ne sont pas planifiés. Pour obtenir des instructions sur l’association de l’étiquette, reportez-vous à [Kubernetes assign label selectors](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) (Kubernetes - Attribuer des sélecteurs d’étiquette).

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Les graphiques de performances n’affichent pas l’UC ou la mémoire des nœuds et des conteneurs sur un cluster non Azure

Les pods d’agent Container insights utilisent le point de terminaison cAdvisor sur l’agent de nœud pour collecter les métriques de performances. Vérifiez que l’agent en conteneur sur le nœud est configuré pour autoriser l’ouverture de `cAdvisor port: 10255` sur tous les nœuds du cluster pour collecter les métriques de performance.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>Les clusters Kubernetes non Azure n’apparaissent pas dans Container insights

Pour visualiser le cluster Kubernetes non Azure dans Container insights, un accès en lecture est requis sur l’espace de travail Log Analytics qui prend en charge cette insight, ainsi que sur la ressource de solution Container Insights **ContainerInsights (*espace de travail*)** .

## <a name="metrics-arent-being-collected"></a>Les métriques ne sont pas collectées

1. Vérifiez que le cluster est dans une [région prise en charge pour les métriques personnalisées](../essentials/metrics-custom-overview.md#supported-regions).

2. Vérifiez que l’attribution de rôle **Éditeur de métriques de surveillance** existe à l’aide de la commande CLI suivante :

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    Pour les clusters avec MSI, l’utilisateur a affecté l’ID client pour les modifications de omsagent chaque fois que la surveillance est activée et désactivée, de sorte que l’attribution de rôle doit exister sur l’ID client MSI actuel. 

3. Pour les clusters avec l’identité pod Azure Active Directory activée et utilisant MSI :

   - Vérifiez que l’étiquette requise **kubernetes.azure.com/managedby: aks** est présente sur les pods omsagent à l’aide de la commande suivante :

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - Vérifiez que les exceptions sont activées lorsque l’identité de pod est activée à l’aide de l’une des méthodes prises en charge dans https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity.

        Exécutez la commande suivante pour vérifier :

        `kubectl get AzurePodIdentityException -A -o yaml`

        La sortie devrait ressembler à ce qui suit :

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```

## <a name="installation-of-azure-monitor-containers-extension-fail-with-an-error-containing-manifests-contain-a-resource-that-already-exists-on-azure-arc-enabled-kubernetes-cluster"></a>Échec de l’installation de l’extension des conteneurs Azure Monitor avec une erreur « Les manifestes contiennent une ressource qui existe déjà » sur le cluster Kubernetes avec Azure Arc
L’erreur _Les manifestes contiennent une ressource qui existe déjà_ indique que des ressources de l’agent Container Insights existent déjà sur le cluster Kubernetes avec Azure Arc. Cela indique que l’agent Container Insights est déjà installé par le biais de chart HELM azuremonitor-containers ou du module complémentaire de supervision s’il s’agit d’un cluster AKS qui est connecté à Azure Arc. La solution à ce problème consiste à nettoyer les ressources existantes de l’agent Container Insights le cas échéant, puis à activer l’extension des conteneurs Azure Monitor.

### <a name="for-non-aks-clusters"></a>Pour les clusters non AKS 
1.  Sur le cluster K8s qui est connecté à Azure Arc, exécutez la commande ci-dessous pour vérifier si la version du chart Helm azmon-containers-release-1 existe ou pas :

    `helm list  -A`

2.  Si la sortie de la commande ci-dessus indique que azmon-containers-release-1 existe, supprimez la version du chart Helm :

    `helm del azmon-containers-release-1`

### <a name="for-aks-clusters"></a>Pour les clusters AKS
1.  Exécutez les commandes ci-dessous et recherchez le profil de module complémentaire omsagent pour vérifier si le module complémentaire de supervision AKS est activé ou pas :

    ```
    az  account set -s <clusterSubscriptionId>
    az aks show -g <clusterResourceGroup> -n <clusterName>
    ```

2.  S’il y a une configuration de profil du module complémentaire omsagent avec l’ID de ressource de l’espace de travail Log Analytics dans la sortie de la commande ci-dessus, cela indique que le module complémentaire de supervision AKS est activé et doit être désactivé :

    `az aks disable-addons -a monitoring -g <clusterResourceGroup> -n <clusterName>`

Si les étapes ci-dessus n’ont pas résolu les problèmes d’installation de l’extension des conteneurs de Azure Monitor, créez un ticket envoyé à Microsoft pour qu’ils soient examinés.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez activer la fonctionnalité de supervision pour collecter des métriques d’intégrité pour les nœuds et pods du cluster AKS et les consulter dans le Portail Azure. Pour savoir comment utiliser Container insights, consultez [Afficher l’intégrité d’Azure Kubernetes Service](container-insights-analyze.md).
