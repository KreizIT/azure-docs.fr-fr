---
title: Chaînes de connexion pour Synapse SQL
description: Chaînes de connexion pour Synapse SQL
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: dfc82cfec8e2c356b0d2fcc3806e21a3bd01a299
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862508"
---
# <a name="connection-strings-for-synapse-sql"></a>Chaînes de connexion pour Synapse SQL

Vous pouvez vous connecter à Synapse SQL avec plusieurs protocoles d’application différents, comme [ADO.NET](/dotnet/framework/data/adonet/), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows), [PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396) et [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). Voici quelques exemples de chaînes de connexion pour chaque protocole. 

Vous pouvez également utiliser le portail Azure pour créer votre chaîne de connexion.  Pour créer votre chaîne de connexion avec le portail Azure, accédez au panneau de votre base de données, sous *Éléments principaux*, sélectionnez *Afficher les chaînes de connexion de la base de données*.

## <a name="sample-adonet-connection-string"></a>Exemple de chaîne de connexion ADO.NET

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Exemple de chaîne de connexion ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Exemple de chaîne de connexion PHP

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Exemple de chaîne de connexion JDBC

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Vous pouvez définir le délai de connexion à 300 secondes pour permettre à la connexion de résister à des courtes périodes d’indisponibilité.

## <a name="recommendations"></a>Recommandations

Pour exécuter des requêtes de **pool SQL serverless**, les outils recommandés sont [Azure Data Studio](get-started-azure-data-studio.md) et Azure Synapse Studio.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à interroger vos données d’analytique avec Visual Studio et d’autres applications, consultez [Soumettre des requêtes avec Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?context=/azure/synapse-analytics/context/context).