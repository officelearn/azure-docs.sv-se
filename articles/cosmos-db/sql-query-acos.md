---
title: ARCCOS i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen arccos (arccosice) i SQL system i Azure Cosmos DB Returnerar vinkeln, i radianer, vars cosinus är det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fd5baa3ffefb7039c40706253e633661a51150f2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873530"
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

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
