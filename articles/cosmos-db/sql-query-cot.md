---
title: COT i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen cotangens (COT) i SQL system i Azure Cosmos DB Returnerar den trigonometriska cotangens för den angivna vinkeln i radianer i det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "78299495"
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
  
  I följande exempel beräknas den `COT` angivna vinkeln.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
