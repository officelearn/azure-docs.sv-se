---
title: COT i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function COT i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d980319730e69fdcb529272ba1b8fb48d2b5b230
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351244"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Returnerar trigonometrisk cotangens för den angivna vinkeln i radianer i det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel beräknas `COT` för den angivna vinkeln.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
