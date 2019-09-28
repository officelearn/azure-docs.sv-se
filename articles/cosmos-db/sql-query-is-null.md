---
title: IS_NULL i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function IS_NULL i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 20486b8f8c0436d264135bb09952345548222216
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349855"
---
# <a name="is_null-azure-cosmos-db"></a>IS_NULL (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger om vilken typ av det angivna uttrycket är null.  
  
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
  
  I följande exempel kontrol leras objekt av JSON Boolean, Number, String, null, Object, array och odefinierade typer med hjälp av funktionen `IS_NULL`.  
  
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

## <a name="next-steps"></a>Nästa steg

- [Typ kontroll funktioner Azure Cosmos DB](sql-query-type-checking-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
