---
title: GeoJSON-dataformat för geofence | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur du förbereder geofence-data som kan användas i MICROSOFT Azure Maps GET- och POST Geofence API.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335624"
---
# <a name="geofencing-geojson-data"></a>Geofencing GeoJSON-data

Azure Maps [GET Geofence](/rest/api/maps/spatial/getgeofence) och [POST Geofence](/rest/api/maps/spatial/postgeofence) API:er kan du hämta närheten till en koordinat i förhållande till en medföljande geofence eller uppsättning staket. I den här artikeln beskrivs hur du förbereder geofence-data som kan användas i Azure Maps GET- och POST API.This article details how to prepare the geofence data that can be used in the Azure Maps GET and POST API.

Data för geofence eller uppsättning geofences `Feature` representeras `FeatureCollection` av `GeoJSON` Objekt och Objekt i format, som definieras i [rfc7946](https://tools.ietf.org/html/rfc7946). Utöver det:

* GeoJSON-objekttypen kan `Feature` vara `FeatureCollection` ett objekt eller ett objekt.
* Geometriobjekttypen kan `Point`vara `MultiPoint` `LineString`en `MultiLineString` `Polygon`, `MultiPolygon`, `GeometryCollection`, , , och .
* Alla funktionsegenskaper `geometryId`ska innehålla en , som används för att identifiera geofence.
* Funktionen `Point`med `MultiPoint` `LineString`, `MultiLineString` , `radius` måste innehålla egenskaper. `radius`mätvärdet mäts i `radius` mätare, varierar värdet från 1 till 10000.
* Funktionen `polygon` med `multipolygon` och geometritypen har ingen radieegenskap.
* `validityTime`är en valfri egenskap som gör att användaren kan ange utgångna tid och giltighetstid för geofence-data. Om inget anges upphör data aldrig att gälla och är alltid giltiga.
* Det `expiredTime` är utgångsdatum och tid för geofencing data. Om värdet `userTime` i begäran är senare än det här värdet betraktas motsvarande geofence-data som utgångna data och efterfrågas inte. På vilken geometriId för dessa geofence data `expiredGeofenceGeometryId` kommer att ingå i matrisen i geofence svar.
* Är `validityPeriod` en lista över giltighetstid för geofence. Om värdet `userTime` i begäran faller utanför giltighetsperioden betraktas motsvarande geofence-data som ogiltiga och kommer inte att efterfrågas. GeometryId för dessa geofence-data `invalidPeriodGeofenceGeometryId` ingår i matrisen i geofence-svaret. I följande tabell visas egenskaperna för validityPeriod-elementet.

| Namn | Typ | Krävs  | Beskrivning |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datumtid  | true | Startdatumtiden för giltighetstiden. |
| endTime   | Datumtid  | true |  Slutdatumtiden för giltighetstiden. |
| återkommandetyp | sträng | false |   Periodens återkommande typ. Värdet kan `Daily`vara `Weekly` `Monthly`, `Yearly`, eller . Standardvärdet `Daily`är .|
| businessDayOnly | Boolean | false |  Ange om data bara är giltiga under arbetsdagar. Standardvärdet `false`är .|


* Alla koordinatvärden representeras som [longitud, latitud] som definieras i `WGS84`.
* För varje funktion, `MultiPoint`som `MultiLineString` `MultiPolygon` innehåller `GeometryCollection`, , eller , används egenskaperna på alla element. till exempel: Alla `MultiPoint` punkter i kommer att använda samma radie för att bilda en multipel cirkel geofence.
* I punktcirkelscenario kan en cirkelgeometri `Point` representeras med hjälp av ett geometriobjekt med egenskaper som utarbetats i [Utöka GeoJSON-geometrier](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Följande är ett prov begäran organ för en geofence representeras `GeoJSON` som en cirkel geofence geometri i att använda en mittpunkt och en radie. Den giltiga perioden för geofence-data börjar från 2018-10-22, 9:00 till 17:00, upprepas varje dag utom för helgen. `expiredTime`anger att dessa geofence-data kommer `userTime` att anses vara `2019-01-01`utgångna, om det i begäran är senare än .  

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
