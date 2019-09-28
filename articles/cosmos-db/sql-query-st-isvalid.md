---
title: ST_ISVALID i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function ST_ISVALID i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
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
   Är ett interjson-, polygon-eller lin Est ring-uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du kontrollerar om en är giltig med hjälp av ST_VALID.  
  
  Nu har exempelvis ett latitud-värde som inte är i det giltiga värdeintervallet [-90, 90], så fråga returnerar FALSKT.  
  
  För polygoner kräver GeoJSON-specifikationen att senaste koordinaten paret som anges ska vara samma som först som skapar en sluten form. Punkter inom en polygon måste anges i motsols ordning. En polygon som angetts i medurs ordning representerar inversen till regionen i den.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Nästa steg

- [Spatiala funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
