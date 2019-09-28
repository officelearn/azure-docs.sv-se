---
title: ARRAY_CONTAINS i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ARRAY_CONTAINS i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247956ccc2718c9bf192b4d704a48014753c00dc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348701"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Returnerar ett booleskt värde som anger huruvida matrisen innehåller det angivna värdet. Du kan söka efter en partiell eller fullständig matchning av ett objekt genom att använda ett booleskt uttryck i kommandot. 

## <a name="syntax"></a>Syntax
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argument
  
*arr_expr*  
   Är mat ris uttrycket som ska genomsökas.  
  
*uttrycks*  
   Är det uttryck som ska hittas.  

*bool_expr*  
   Är ett booleskt uttryck. Om den utvärderas till true och om det angivna sökvärdet är ett objekt, söker kommandot efter en partiell matchning (sökobjektet är en delmängd av ett av objekten). Om den utvärderas till false söker kommandot efter en fullständig matchning av alla objekt i matrisen. Standardvärdet om inget värde anges är falskt. 
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt värde.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kan du söka efter medlemskap i en matris med hjälp av `ARRAY_CONTAINS`.  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"b1": true, "b2": false}]  
```  

I följande exempel hur du söker efter en partiell matchning av en JSON i en matris med ARRAY_CONTAINS.  
  
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
  

## <a name="next-steps"></a>Nästa steg

- [Array Functions Azure Cosmos DB](sql-query-array-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
