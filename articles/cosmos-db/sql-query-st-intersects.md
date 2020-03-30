---
title: ST_INTERSECTS i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion ST_INTERSECTS i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303145"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Returnerar ett booleskt uttryck som anger om det GeoJSON-objekt (Punkt, Polygon eller LineString) som anges i det första argumentet skär geojson (punkt, polygon eller radsträng) i det andra argumentet.  
  
## <a name="syntax"></a>Syntax
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*spatial_expr*  
   Är ett GeoJSON-punkt-, polygon- eller linestring-objektuttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett booleskt värde.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du hittar alla områden som korsar den angivna polygonen.  
  
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

Denna systemfunktion kommer att dra nytta av ett [geospatialt index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Nästa steg

- [Rumsliga funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
