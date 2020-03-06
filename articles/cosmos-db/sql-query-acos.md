---
title: ARCCOS i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen arccos (arccosice) i SQL system i Azure Cosmos DB Returnerar vinkeln, i radianer, vars cosinus är det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300969"
---
# <a name="acos-azure-cosmos-db"></a>ARCCOS (Azure Cosmos DB)
 Returnerar vinkeln i radianer vars cosinus är det angivna numeriska uttrycket. Kallas även arccosinus.  
  
## <a name="syntax"></a>Syntax
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras `ACOS`-1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
