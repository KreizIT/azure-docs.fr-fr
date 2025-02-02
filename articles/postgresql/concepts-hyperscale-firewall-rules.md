---
title: Accès public – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Cet article décrit l’accès public pour Azure Database pour PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: cb695514fe4fd1b3d0ed72dd70aeb8b5d6ca4253
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072179"
---
# <a name="public-access-in-azure-database-for-postgresql---hyperscale-citus"></a>Accès public dans Azure Database pour PostgreSQL - Hyperscale (Citus)

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

Cette page décrit l’option d’accès public. Pour l’accès privé, voir [ici](concepts-hyperscale-private-access.md).

## <a name="firewall-overview"></a>Présentation du pare-feu

Le pare-feu du serveur Azure Database pour PostgreSQL empêche tout accès à votre nœud coordinateur Hyperscale (Citus) tant que vous n’avez pas spécifié les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès au serveur en fonction de l’adresse IP d’origine de chaque demande.
Pour configurer votre pare-feu, vous créez des règles de pare-feu qui spécifient les plages d’adresses IP acceptables. Vous pouvez créer des règles de pare-feu au niveau du serveur.

**Règles de pare-feu :** ces règles permettent aux clients d’accéder à votre nœud coordinateur Hyperscale (Citus), c’est-à-dire à toutes les bases de données dans le même serveur logique. Les règles de pare-feu au niveau du serveur peuvent être configurées à l’aide du portail Azure. Pour créer des règles de pare-feu au niveau du serveur, vous devez être le propriétaire de l’abonnement ou collaborateur.

Par défaut, tous les accès de base de données à votre nœud coordinateur sont bloqués par le pare-feu. Pour pouvoir utiliser votre serveur à partir d’un autre ordinateur, vous devez spécifier une ou plusieurs règles de pare-feu au niveau du serveur afin de permettre l’accès à votre serveur. Utilisez les règles de pare-feu pour spécifier les plages d’adresses IP d’Internet à autoriser. L’accès au site web du Portail Azure proprement dit n’est pas affecté par les règles de pare-feu.
Les tentatives de connexion à partir d’Internet et d’Azure doivent franchir le pare-feu pour pouvoir atteindre votre base de données PostgreSQL, comme l’illustre le diagramme suivant :

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="Exemple de flux de fonctionnement du pare-feu":::

## <a name="connecting-from-the-internet-and-from-azure"></a>Connexion à partir d’Internet et d’Azure

Un pare-feu de groupe de serveurs Hyperscale (Citus) contrôle les utilisateurs pouvant se connecter au nœud coordinateur du groupe. Le pare-feu détermine l’accès en consultant une liste de règles configurable. Chaque règle est une adresse IP ou une plage d’adresses autorisée.

Quand le pare-feu bloque les connexions, il peut provoquer des erreurs d’application. L’utilisation du pilote JDBC PostgreSQL, par exemple, génère une erreur de ce type :

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

Consultez [Créer et gérer des règles de pare-feu](howto-hyperscale-manage-firewall-using-portal.md) pour savoir comment les règles sont définies.

## <a name="troubleshooting-the-database-server-firewall"></a>Dépannage du pare-feu du serveur de base de données
Considérez les points suivants quand l’accès au service Microsoft Azure Database pour PostgreSQL - Hyperscale (Citus) présente un comportement anormal :

* **Les modifications apportées à la liste d’approbation n’ont pas encore pris effet :** jusqu’à cinq minutes peuvent s’écouler avant que les modifications apportées à la configuration du pare-feu Hyperscale (Citus) soient effectives.

* **L’utilisateur n’est pas autorisé ou un mot de passe incorrect a été utilisé :** si un utilisateur n’a pas d’autorisation sur le serveur ou si le mot de passe utilisé est incorrect, la connexion au serveur est refusée. Créer un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur ; chaque client doit tout de même fournir les informations d’identification de sécurité nécessaires.

Par exemple, avec un client JDBC, l’erreur suivante est susceptible d’apparaître.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **Adresse IP dynamique :** Si vous avez une connexion Internet avec un adressage IP dynamique et que le pare-feu demeure infranchissable, vous pouvez essayer une des solutions suivantes :

* Demandez à votre fournisseur d’accès Internet la plage d’adresses IP attribuée à vos ordinateurs clients qui accèdent au nœud coordinateur Hyperscale (Citus), puis ajoutez cette plage dans une règle de pare-feu.

* Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez l’adresse IP statique en tant que règle de pare-feu.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations sur la création de règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez les articles suivants :
* [Créer et gérer des règles de pare-feu de la base de données Azure pour PostgreSQL à l’aide du Portail Azure](howto-hyperscale-manage-firewall-using-portal.md)
