---
title: ARRAY_CONTAINS i Azure Cosmos DB-frågespråk
description: Lär dig mer om hur matrisen innehåller SQL-systemfunktionen i Azure Cosmos DB returnerar en boolesk som anger om matrisen innehåller det angivna värdet
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303485"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Returnerar ett booleskt värde som anger huruvida matrisen innehåller det angivna värdet. Du kan söka efter en partiell eller fullständig matchning av ett objekt med hjälp av ett booleskt uttryck i kommandot. 

## <a name="syntax"></a>Syntax
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argument
  
*arr_expr*  
   Är matrisuttrycket som ska sökas igenom.  
  
*Uttryck*  
   Är det uttryck som ska hittas.  

*bool_expr*  
   Är ett booleskt uttryck. Om den utvärderas till "true" och om det angivna sökvärdet är ett objekt, söker kommandot efter en partiell matchning (sökobjektet är en delmängd av ett av objekten). Om det utvärderas till "false" söker kommandot efter en fullständig matchning av alla objekt i matrisen. Standardvärdet om det inte anges är falskt. 
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett booleskt värde.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel söker du efter `ARRAY_CONTAINS`medlemskap i en matris med .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"b1": true, "b2": false}]  
```  

I följande exempel söker du efter en partiell matchning av en JSON i en matris med ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Anmärkningar

Denna systemfunktion kommer att dra nytta av ett [intervallindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Matrisfunktioner Azure Cosmos DB](sql-query-array-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
