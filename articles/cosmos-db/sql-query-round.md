---
title: Avrunda i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ROUND i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 41b1372cd5165b3548a4e574e7eb037111188bac
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341560"
---
# <a name="round-azure-cosmos-db"></a>Avrunda (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar ett numeriskt värde avrundat till närmaste heltal.  
  
## <a name="syntax"></a>Syntax
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
  Avrundnings åtgärden som utförs följer följande mitt punkts avrundning bort från noll. Om indatatypen är ett numeriskt uttryck som är exakt mellan två heltal blir resultatet det närmaste heltal svärdet bort från noll. Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).
  
  |<numeric_expr>|Rektangel|
  |-|-|
  |– 6,5000|-7|
  |– 0,5|-1|
  |0,5|1|
  |6,5000|7||
  
## <a name="examples"></a>Exempel
  
  I följande exempel avrundas följande positiva och negativa tal till närmaste heltal.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Här är resultatuppsättningen.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
