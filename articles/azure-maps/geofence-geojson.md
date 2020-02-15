---
title: Geografiskt JSON-dataformat för geografiskt avgränsnings tecken | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du förbereder de avgränsnings data som kan användas i Microsoft Azure Maps GET-och POST-API.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7d1c9a1587771a020f5c9f89e2497a25eb1bba70
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210029"
---
# <a name="geofencing-geojson-data"></a>Polystaket, polyjson-data

Med API: erna Azure Maps [Hämta gräns](/rest/api/maps/spatial/getgeofence) -och [postbaserade gränser](/rest/api/maps/spatial/postgeofence) kan du hämta närhet av en koordinat i förhållande till ett givet geografiskt avgränsnings tecken eller en uppsättning avgränsningar. Den här artikeln beskriver hur du förbereder de avgränsnings data som kan användas i Azure Maps GET-och POST-API.

Data för avgränsning eller uppsättning av gränser representeras av `Feature` objekt och `FeatureCollection` objekt i `GeoJSON` format, som definieras i [rfc7946](https://tools.ietf.org/html/rfc7946). Förutom det:

* Typen av typen interjson kan vara ett `Feature`-objekt eller ett `FeatureCollection`-objekt.
* Geometri objekt typen kan vara en `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`och `GeometryCollection`.
* Alla funktions egenskaper måste innehålla en `geometryId`som används för att identifiera den här gränsen.
* Funktionen med `Point`, `MultiPoint``LineString``MultiLineString` måste innehålla `radius` i egenskaper. `radius` svärdet mäts i meter, `radius` värde intervallet från 1 till 10000.
* Funktionen med `polygon` och `multipolygon` geometri typen har ingen RADIUS-egenskap.
* `validityTime` är en valfri egenskap som låter användaren ange utgångna tid och giltighets tid för de olika gräns uppgifterna. Om detta inte anges upphör data aldrig att gälla och är alltid giltigt.
* `expiredTime` är utgångs datum och-tid för avgränsning av data. Om värdet för `userTime` i begäran är senare än det här värdet betraktas motsvarande avgränsnings data som utgångna data och inte efter frågas. Då kommer geometryId för dessa avgränsnings data att inkluderas i `expiredGeofenceGeometryId` matris inom ett gräns värde.
* `validityPeriod` är en lista över tids perioden för giltighets tiden för den här gränsen. Om värdet för `userTime` i begäran faller utanför giltighets perioden, betraktas motsvarande avgränsnings information som ogiltig och kommer inte att frågas. GeometryId för dessa avgränsnings data ingår i `invalidPeriodGeofenceGeometryId` matris inom ett gräns värde. I följande tabell visas egenskaperna för validityPeriod-elementet.

| Namn | Typ | Krävs  | Beskrivning |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datum/tid  | true | Start datum tiden för giltighets tids perioden. |
| endTime   | Datum/tid  | true |  Slutdatumet för giltighets tids perioden. |
| recurrenceType | sträng | false |   Periodens upprepnings typ. Värdet kan vara `Daily`, `Weekly`, `Monthly`eller `Yearly`. Standardvärdet är `Daily`.|
| businessDayOnly | Boolesk | false |  Ange om data endast är giltiga under arbets dagar. Standardvärdet är `false`.|


* Alla koordinater-värden representeras som [longitud, latitud] som definieras i `WGS84`.
* För varje funktion, som innehåller `MultiPoint`, `MultiLineString`, `MultiPolygon` eller `GeometryCollection`, används egenskaperna för alla element. exempel: alla punkter i `MultiPoint` använder samma radie för att bilda ett geografiskt avgränsnings tecken på flera cirklar.
* I punkt-cirkel-scenariot kan en cirkel geometri representeras med hjälp av ett `Point` Geometry-objekt med egenskaper som har [utökats i utökning av Geometries](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Följande är en exempel begär ande text för ett område som representeras som en cirkels geometrisk geometri i `GeoJSON` att använda en mitt punkt och en radie. Den giltiga tids perioden för avgränsnings data börjar från 2018-10-22, 9 till 17, upprepas varje dag förutom helgen. `expiredTime` indikerar att dessa gräns data kommer att anses vara förfallna, om `userTime` i begäran är senare än `2019-01-01`.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
