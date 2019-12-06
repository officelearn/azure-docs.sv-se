---
title: ARCSin i Azure Cosmos DB frågespråk
description: Lär dig mer om hur arcus sinus-funktionen (ARCSin) SQL system i Azure Cosmos DB Returnerar vinkeln, i radianer, vars sinus är det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871746"
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
  
  I följande exempel returneras `ASIN`-1.  
  
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
