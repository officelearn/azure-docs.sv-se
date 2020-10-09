---
title: ST_ISVALID i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen ST_ISVALID i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
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
  
  I följande exempel visas hur du kontrollerar om en punkt är giltig med ST_VALID.  
  
  Till exempel har den här punkten ett latitud-värde som inte finns i det giltiga värde intervallet [-90, 90], så frågan returnerar false.  
  
  För polygoner kräver polyjson-specifikationen att det sista koordinat paret som anges ska vara detsamma som det första, för att skapa en sluten form. Punkter inom en polygon måste anges i en motsols ordning. En polygon som anges i medsols ordning representerar inversen för regionen i den.  
  
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
