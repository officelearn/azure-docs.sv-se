---
title: IS_BOOL i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen IS_BOOL i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b7f1cfb09121309e246b314d57a5e4e475bd0983
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303876"
---
# <a name="is_bool-azure-cosmos-db"></a>IS_BOOL (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett booleskt värde.  
  
## <a name="syntax"></a>Syntax
  
```sql
IS_BOOL(<expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*uttrycks*  
   Är ett valfritt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrol leras objekt av JSON Boolean, Number, String, null, Object, array och odefinierade typer med hjälp av `IS_BOOL` funktionen.  
  
```sql
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Typ kontroll funktioner Azure Cosmos DB](sql-query-type-checking-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
