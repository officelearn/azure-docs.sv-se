---
title: IS_NULL i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen IS_NULL i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1b6b91c7d76fa44036185b896fd7ee64ded89020
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088292"
---
# <a name="is_null-azure-cosmos-db"></a>IS_NULL (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är null.  
  
## <a name="syntax"></a>Syntax
  
```sql
IS_NULL(<expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*uttrycks*  
   Är ett valfritt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrol leras objekt av JSON Boolean, Number, String, null, Object, array och odefinierade typer med hjälp av `IS_NULL` funktionen.  
  
```sql
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Typ kontroll funktioner Azure Cosmos DB](sql-query-type-checking-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
