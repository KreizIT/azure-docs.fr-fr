---
title: 'Langage de requête Azure Cosmos DB : LOG'
description: Découvrez la fonction système SQL LOG dans Azure Cosmos DB, qui retourne le logarithme naturel de l’expression numérique spécifiée
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 03b5c27fc0f4e149847ea10c9c1ab3c5a8d55251
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122525833"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Retourne le logarithme naturel de l'expression numérique spécifiée.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Arguments
  
*numeric_expr*  
   Est une expression numérique.  
  
*base*  
   L’argument numérique facultatif qui définit la base du logarithme.  
  
## <a name="return-types"></a>Types de retour
  
  Renvoie une expression numérique.  
  
## <a name="remarks"></a>Notes
  
  Par défaut, LOG() renvoie le logarithme naturel. Vous pouvez modifier la base du logarithme avec une autre valeur en utilisant le paramètre de base facultatif.  
  
  Le logarithme népérien est le logarithme en base **e**, où **e** est une constante irrationnelle environ égale à 2,718281828.  
  
  Le logarithme naturel de la valeur exponentielle d’un nombre est le nombre lui-même : LOG (EXP (n)) = n. Et la valeur exponentielle du logarithme naturel d’un nombre est le nombre lui-même : EXP (LOG (n)) = n.

  Cette fonction système n’utilisera pas l’index.
  
## <a name="examples"></a>Exemples
  
  L’exemple suivant déclare une variable et renvoie la valeur du logarithme de la variable spécifiée (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 L’exemple suivant calcule la valeur `LOG` pour l’exposant d’un nombre.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Voici le jeu de résultats obtenu.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Fonctions mathématiques Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Fonctions système Azure Cosmos DB](sql-query-system-functions.md)
- [Présentation d’Azure Cosmos DB](../introduction.md)
