---
title: ABS i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ABS i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 480c5ffcfaccfac5061e53612a2ee57235cfa626
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348752"
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
