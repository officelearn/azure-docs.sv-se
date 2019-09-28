---
title: GRADER i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function-grader i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8c2109c2c11f137b1966741a95d1d0c02895016
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351194"
---
# <a name="degrees-azure-cosmos-db"></a>GRADER (Azure Cosmos DB)
 Returnerar motsvarande vinkel i grader för en vinkel som anges i radianer.  
  
## <a name="syntax"></a>Syntax
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras grad i en vinkeln för PI/2 radianer.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
