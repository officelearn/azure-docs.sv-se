---
title: ARCSin i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ARCSin i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5a64fc8d8f87d38f001bf2bc9dd581692c97fe64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348504"
---
# <a name="asin-azure-cosmos-db"></a>ARCSin (Azure Cosmos DB)
 Returnerar vinkeln i radianer vars sinus är det angivna numeriska uttrycket. Detta kallas också arcsinus.  
  
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
  
  I följande exempel returneras `ASIN` av-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
