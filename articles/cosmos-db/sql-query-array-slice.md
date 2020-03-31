---
title: ARRAY_SLICE i Azure Cosmos DB-frågespråk
description: Lär dig mer om hur funktionen Matrissegment SQL-system i Azure Cosmos DB returnerar en del av ett matrisuttryck
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303332"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Returnerar en del av ett matrisuttryck.
  
## <a name="syntax"></a>Syntax
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argument
  
*arr_expr*  
   Är alla matrisuttryck.  
  
*num_expr*  
   Nollbaserat numeriskt index som matrisen ska börja med. Negativa värden kan användas för att ange startindexet i förhållande till det sista elementet i matrisen, dvs -1 refererar till det sista elementet i matrisen.  

*num_expr* Valfrit numeriskt uttryck som anger det maximala antalet element i den resulterande matrisen.    

## <a name="return-types"></a>Returtyper
  
  Returnerar ett arrayuttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du hämtar `ARRAY_SLICE`olika segment i en matris med .  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matrisfunktioner Azure Cosmos DB](sql-query-array-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
