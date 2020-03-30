---
title: ST_DISTANCE i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion ST_DISTANCE i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537303"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Returnerar avståndet mellan de två geojsonpunkts-, polygon-, multipolygon- eller radsträngsuttrycken. Mer information finns i artikeln [Geospatial och GeoJSON-platsdata.](sql-query-geospatial-intro.md)
  
## <a name="syntax"></a>Syntax
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*spatial_expr*  
   Är ett giltigt geojson-punkt-, polygon- eller linestring-objektuttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck som innehåller avståndet. Detta uttrycks i mätare för standardreferenssystemet.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du returnerar alla familjedokument som finns `ST_DISTANCE` inom 30 km från den angivna platsen med hjälp av den inbyggda funktionen. .  
  
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

## <a name="remarks"></a>Anmärkningar

Denna systemfunktion kommer att dra nytta av ett [geospatialt index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Nästa steg

- [Rumsliga funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
