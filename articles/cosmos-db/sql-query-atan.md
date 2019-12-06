---
title: ARCTAN i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen arcus tangens (arctan) i SQL system i Azure Cosmos DB Returnerar vinkeln, i radianer, vars tangens är det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fd398e343a3e617ceb9f49ca8f31ee776a7f073
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873462"
---
# <a name="atan-azure-cosmos-db"></a>ARCTAN (Azure Cosmos DB)
 Returnerar vinkeln i radianer vars tangent är det angivna numeriska uttrycket. Detta kallas även arctangens.  
  
## <a name="syntax"></a>Syntax
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras `ATAN` för det angivna värdet.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
