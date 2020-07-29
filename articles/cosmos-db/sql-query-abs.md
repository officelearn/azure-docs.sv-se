---
title: ABS i Azure Cosmos DB frågespråk
description: Lär dig mer om hur den absoluta (ABS) SQL system-funktionen i Azure Cosmos DB Returnerar det positiva värdet för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6d173827f2695cc20fa208d390731acf0edb3848
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78301105"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 Returnerar det absoluta (positiva) värdet för det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas resultatet av att använda `ABS` funktionen på tre olika tal.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
