---
title: ARCSin i Azure Cosmos DB frågespråk
description: Lär dig mer om hur arcus sinus-funktionen (ARCSin) SQL system i Azure Cosmos DB Returnerar vinkeln, i radianer, vars sinus är det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302703"
---
# <a name="asin-azure-cosmos-db"></a>ARCSin (Azure Cosmos DB)
 Returnerar vinkeln i radianer vars sinus är det angivna numeriska uttrycket. Detta kallas även arcus sinus.  
  
## <a name="syntax"></a>Syntax
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras `ASIN` -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
