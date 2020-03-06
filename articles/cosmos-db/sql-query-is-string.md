---
title: IS_STRING i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen IS_STRING i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a717f343b0f46522a3ce2bb56c32e3f15998d777
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303774"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger om det angivna uttrycket är en sträng.  
  
## <a name="syntax"></a>Syntax
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*uttrycks*  
   Är ett valfritt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrol leras objekt av JSON Boolean, Number, String, null, Object, array och odefinierade typer med hjälp av funktionen `IS_STRING`.  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Typ kontroll funktioner Azure Cosmos DB](sql-query-type-checking-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
