---
title: Format för Geofence GeoJSON-data i Azure Maps | Microsoft Docs
description: Lär dig mer om format för Geofence GeoJSON-data i Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: aa39661c8ecc4b594478def7d0cc9be3006294c4
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008253"
---
# <a name="geofencing-geojson-data"></a>Geofencing GeoJSON-data

Azure Maps [geofence-området hämta](https://docs.microsoft.com/rest/api/map/spatial/getgeofencepreview) och [POST geofence-området](https://docs.microsoft.com/rest/api/map/spatial/postgeofencepreview) API: er låter dig hämta närhet av en koordinat i förhållande till en angiven geofence-området eller uppsättning avgränsningstecken. Den här artikeln beskriver hur du förbereder geofence-området-data som kan användas i Azure Maps få och publicera API: et.

Data för geofence-området eller uppsättning geofence-områdena representeras av `Feature` objekt och `FeatureCollection` objekt i `GeoJSON` format, som definieras i [rfc7946](https://tools.ietf.org/html/rfc7946). Förutom den:

* GeoJSON-objekttypen kan vara en `Feature` objekt eller en `FeatureCollection` objekt.
* Objekttyp för geometri kan vara en `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`, och `GeometryCollection`.
* Alla egenskaper för funktionen ska innehålla en `geometryId`, som används för att identifiera geofence-området.
* Funktionen med `Point`, `MultiPoint`, `LineString`, `MultiLineString` måste innehålla `radius` i egenskaperna. `radius` värdet mäts i meter, den `radius` intervall från 1 till 10000.
* Funktionen med `polygon` och `multipolygon` geometri typen har inte en radius-egenskap.
* `validityTime` en valfri egenskap som användaren kan är ange förfallotid och giltighet tidsperiod för geofence-området data. Om inte anges data aldrig upphör att gälla och alltid är giltig.
* Den `expiredTime` är datum och tid för geofencing data. Om värdet för `userTime` i begäran är senare än detta värde, motsvarande geofence-området data anses som har upphört att gälla data och inte frågas. Som geometryId av geofence-området data kommer att inkluderas i `expiredGeofenceGeometryId` matris inom geofence-området svaret.
* Den `validityPeriod` är en lista över giltigheten tidsperioden för geofence-området. Om värdet för `userTime` i begäran infaller under utanför giltighetsperioden, motsvarande geofence-området data anses som ogiltig och inte efterfrågas. GeometryId geofence-området data som ingår i `invalidPeriodGeofenceGeometryId` matris inom geofence-området svar. I följande tabell visas egenskaperna för validityPeriod elementet.

| Namn | Type | Obligatoriskt  | Beskrivning |
| :------------ |:------------: |:---------------:| :-----|
| startTime | DateTime  | true | Starta datum-tid för giltigheten tidsperiod. |
| endTime   | DateTime  | true |  Slutar datum-tid för giltigheten tidsperiod. |
| värde för recurrenceType | sträng | false |   Intervalltyp är slut. Värdet kan vara `Daily`, `Weekly`, `Monthly`, eller `Yearly`. Standardvärdet är `Daily`.|
| businessDayOnly | Boolesk | false |  Ange om data är endast giltig under arbetsdagar. Standardvärdet är `false`.|


* Alla koordinaten värden representeras som [latitud, longitud] definierad i `WGS84`.
* För varje funktion som innehåller `MultiPoint`, `MultiLineString`, `MultiPolygon` , eller `GeometryCollection`, egenskaperna tillämpas på alla element. Till exempel: Alla punkter i `MultiPoint` kommer att använda samma radius för att bilda en flera cirkel geofence-området.
* I punkt cirkel scenariot en cirkel geometry kan representeras med hjälp av en `Point` geometri objekt med egenskaper som utvecklas i [utöka GeoJSON geometrier](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Följande är ett exempel förfrågans brödtext för geofence-området representeras som en cirkel geofence-området geometri i `GeoJSON` med hjälp av en central punkt och en radius. Giltighetsperiod geofence-området data börjar från 2018-10-22, 9: 00 till 17: 00, upprepas varje dag förutom helgen. `expiredTime` Anger den här geofence-området data betraktas som upphört att gälla, om `userTime` i begäran är senare än `2019-01-01`.  

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