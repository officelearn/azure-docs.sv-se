---
title: ST_ISVALIDDETAILED i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen ST_ISVALIDDETAILED i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349349"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Returnerar ett JSON-värde som innehåller ett booleskt värde om det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt, och orsaken som ett strängvärde om det är ogiltigt.  
  
## <a name="syntax"></a>Syntax
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*spatial_expr*  
   Är ett uttryck för en interjson-punkt eller ett polygon.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett JSON-värde som innehåller ett booleskt värde om det angivna värdet för ett interjson-eller polygon-uttryck är giltigt och om det är ogiltigt, och även orsaken som ett sträng värde.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrol lera giltighet (med information) med hjälp av `ST_ISVALIDDETAILED` .  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Spatiala funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
