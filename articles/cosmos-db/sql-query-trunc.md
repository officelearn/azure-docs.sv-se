---
title: TRUNC i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function TRUNC i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e9004db8f8e4ca3f4e3afd0600bc0abd78b1b754
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349085"
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
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel trunkerar följande positiva och negativa tal till närmaste heltal.  
  
```sql
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
