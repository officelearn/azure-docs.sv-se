---
title: ATN2 i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ATN2 i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43bf2f6e27d093b72560b87349150268e0f58a60
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350190"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Returnerar huvudvärdet arctangens av y / x, uttryckt i radianer.  
  
## <a name="syntax"></a>Syntax
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel beräknar ATN2 för den angivna x- och y komponenter.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
