---
title: COS i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function COS i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e64127b7af749af87b90331c3f175957cfa7f09
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351080"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Returnerar trigonometrisk cosinus för den angivna vinkeln i radianer i det angivna uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel beräknas `COS` för den angivna vinkeln.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
