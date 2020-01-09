---
title: TAK i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen tak SQL system i Azure Cosmos DB Returnerar det minsta heltal som är större än eller lika med det angivna numeriska uttrycket.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2da7820a6c9f1f90585b4deb605bb99c7580b0e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444805"
---
# <a name="ceiling-azure-cosmos-db"></a>TAK (Azure Cosmos DB)
 Returnerar det minsta heltalsvärdet som är större än eller lika med det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas positiva numeriska värden, negativa värden och nollvärden med funktionen `CEILING`.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
