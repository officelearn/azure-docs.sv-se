---
title: COT i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen cotangens (COT) i SQL system i Azure Cosmos DB Returnerar den trigonometriska cotangens för den angivna vinkeln i radianer i det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f049d1295eef3e6a45abeaafe8c22d376f90abe1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871507"
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
  
  I följande exempel beräknas `COT` av den angivna vinkeln.  
  
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
