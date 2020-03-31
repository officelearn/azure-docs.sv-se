---
title: ST_ISVALID i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion ST_ISVALID i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349358"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger huruvida det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt.  
  
## <a name="syntax"></a>Syntax
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*spatial_expr*  
   Är ett GeoJSON Point-, Polygon- eller LineString-uttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du kontrollerar om en punkt är giltig med ST_VALID.  
  
  Den här punkten har till exempel ett latitudvärde som inte finns i det giltiga värdeintervallet [-90, 90], så frågan returnerar false.  
  
  För polygoner kräver GeoJSON-specifikationen att det senast angivna koordinatparet ska vara detsamma som det första för att skapa en sluten form. Punkter inom en polygon måste anges i moturs ordning. En polygon som anges i medurs ordning representerar inversen av regionen inom den.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Nästa steg

- [Rumsliga funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
