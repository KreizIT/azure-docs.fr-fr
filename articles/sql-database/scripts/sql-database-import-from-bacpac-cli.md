---
title: 'Azure CLI : Importer un fichier BACPAC vers une base de données dans Azure SQL Database'
description: Exemple de script Azure CLI permettant d’importer un fichier BACPAC dans une base de données dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: load & move data, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 05/24/2019
ms.openlocfilehash: 3a43ef2527248a533a7511a34661e058e876c9c5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730948"
---
# <a name="use-cli-to-import-a-bacpac-file-into-a-database-in-sql-database"></a>Utiliser CLI pour importer un fichier BACPAC dans une base de données dans SQL Database

Cet exemple de script Azure CLI importe une base de données d’un fichier *.bacpac* vers une base de données dans SQL Database.  

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemple de script

### <a name="sign-in-to-azure"></a>Connexion à Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Exécuter le script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/import-from-bacpac/import-from-bacpac.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Nettoyer le déploiement

Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informations de référence sur l’exemple

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Description |
|---|---|
| [az sql server](/cli/azure/sql/server) | Commandes de serveur. |
| [az sql db import](/cli/azure/sql/db#az_sql_db_import) | Commande d’importation de base de données. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database dans [Documentation Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
