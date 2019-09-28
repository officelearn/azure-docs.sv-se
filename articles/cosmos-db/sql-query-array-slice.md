---
title: ARRAY_SLICE i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ARRAY_SLICE i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1df4177bb8b56bc98977af0f5180e8df5affb257
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348544"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Returnerar en del av ett matrisuttryck.
  
## <a name="syntax"></a>Syntax
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argument
  
*arr_expr*  
   Är ett mat ris uttryck.  
  
*num_expr*  
   Nollbaserat numeriskt index som du vill börja matrisen. Negativa värden kan användas för att ange startIndex i förhållande till det sista elementet i matrisen d.v.s. -1-referenser till det sista elementet i matrisen.  

*num_expr* Valfritt numeriskt uttryck som anger det maximala antalet element i den resulterande matrisen.    

## <a name="return-types"></a>Retur typer
  
  Returnerar ett matrisuttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du får olika segment i en matris med `ARRAY_SLICE`.  
  
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

## <a name="next-steps"></a>Nästa steg

- [Array Functions Azure Cosmos DB](sql-query-array-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
