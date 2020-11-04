---
title: ST_DISTANCE i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen ST_DISTANCE i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d486f5c9a4ccc1737a051762f64bc712192df44
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335287"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar avståndet mellan två polyjson-punkter, polygoner, multipolygon-eller lin Est ring-uttryck. Mer information finns i artikeln om [geospatiala och geospatiala JSON-platser](sql-query-geospatial-intro.md) .
  
## <a name="syntax"></a>Syntax
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*spatial_expr*  
   Är ett giltigt objekt uttryck för en interjson-punkt, polygon eller lin Est ring.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck som innehåller avståndet. Detta uttrycks i mätare för standard referens systemet.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du returnerar alla familje dokument som ligger inom 30 km från den angivna platsen med hjälp av den `ST_DISTANCE` inbyggda funktionen. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [geospatialt index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Nästa steg

- [Spatiala funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
