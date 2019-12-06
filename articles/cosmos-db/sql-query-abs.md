---
title: ABS i Azure Cosmos DB frågespråk
description: Lär dig mer om hur den absoluta (ABS) SQL system-funktionen i Azure Cosmos DB Returnerar det positiva värdet för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70648dcf28d760c3a81c0cb426cd9b2d3ce96fff
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871864"
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
  
  I följande exempel visas resultatet av att använda funktionen `ABS` på tre olika tal.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
