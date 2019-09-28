---
title: STARTSWITH i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function STARTSWITH i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a7822425f17d6e121dfcb20d8766f0b3bc7032a2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349310"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger om först stränguttryck börjar med andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är ett sträng uttryck.
  
*str_expr2*  
   Är ett sträng uttryck som ska jämföras med början av *str_expr1*.

## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrollerar om strängen ”abc” börjar med ”b” och ”a”.  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
