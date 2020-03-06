---
title: COS i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen cosinus (COS) i SQL system i Azure Cosmos DB Returnerar det trigonometriska cosinus för den angivna vinkeln, i radianer, i det angivna uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304029"
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
  
  I följande exempel beräknas `COS` av den angivna vinkeln.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
