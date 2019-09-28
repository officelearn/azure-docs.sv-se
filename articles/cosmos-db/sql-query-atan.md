---
title: ARCTAN i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ARCTAN i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ee3867f3a4938358e8d61aa8a98f747756ee3e8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348561"
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
