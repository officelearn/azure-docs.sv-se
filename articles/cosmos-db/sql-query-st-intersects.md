---
title: ST_INTERSECTS i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen ST_INTERSECTS i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303145"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Returnerar ett booleskt uttryck som anger om GeoJSON (punkt, Polygon eller LineString) det angivna objektet i det första argumentet korsar GeoJSON (punkt, Polygon eller LineString) i det andra argumentet.  
  
## <a name="syntax"></a>Syntax
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*spatial_expr*  
   Är ett uttryck för en interjson-punkt, polygon eller lin Est ring-objekt.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt värde.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du hittar alla områden som överlappar angivna polygonen.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer att ha nytta av ett [geospatialt index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Nästa steg

- [Spatiala funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
