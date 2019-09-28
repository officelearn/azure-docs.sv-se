---
title: ARCCOS i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function arccos i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c9d29fba6b5dc55a98bf90cfafe0940d7bf9674
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348739"
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
  
  I följande exempel returneras `ACOS` av-1.  
  
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
