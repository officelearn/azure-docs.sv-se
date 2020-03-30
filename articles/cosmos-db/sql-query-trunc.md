---
title: KORTI-kort i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionSkortkort i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8bad33f593bae2679c83d59ae4567dcab4a64809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304233"
---
# <a name="trunc-azure-cosmos-db"></a>TRUNC (Azure Cosmos DB)
 Returnerar ett numeriskt värde trunkerat till närmaste heltal.  
  
## <a name="syntax"></a>Syntax
  
```sql
TRUNC(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel trunkeras följande positiva och negativa tal till närmaste heltalsvärde.  
  
```sql
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a name="remarks"></a>Anmärkningar

Denna systemfunktion kommer att dra nytta av ett [intervallindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
