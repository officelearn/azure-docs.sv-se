---
title: Avrunda i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ROUND i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9712aedd3d6748f3bceea67a3270b6c080cc16f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88794286"
---
# <a name="round-azure-cosmos-db"></a>Avrunda (Azure Cosmos DB)
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
