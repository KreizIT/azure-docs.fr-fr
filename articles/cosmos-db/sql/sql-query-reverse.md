---
title: 'Langage de requête Azure Cosmos DB : REVERSE'
description: Découvrez la fonction système SQL REVERSE dans Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b530102bfab6605800d2e33306b600922a2cdcfc
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533317"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne l'ordre inverse d'une valeur de chaîne.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Arguments
  
*str_expr*  
   Est une expression de chaîne.  
  
## <a name="return-types"></a>Types de retour
  
  Retourne une expression de chaîne.  
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant montre comment utiliser `REVERSE` dans une requête.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Notes

Cette fonction système n’utilisera pas l’index.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions de chaîne Azure Cosmos DB](sql-query-string-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
