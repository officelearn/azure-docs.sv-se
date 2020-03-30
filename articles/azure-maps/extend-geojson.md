---
title: Utökade GeoJSON-geometrier | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur Microsoft Azure Maps utökar GeoJSON-specifiken för att representera vissa geometrier.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276405"
---
# <a name="extended-geojson-geometries"></a>Utökade GeoJSON-geometrier

Azure Maps innehåller en lista över kraftfulla API:er för att söka inuti och längs geografiska funktioner. Dessa API:er följer standarden [GeoJSON spec][1] för att representera geografiska funktioner.  

[GeoJSON-specifikationen][1] stöder endast följande geometrier:

* GeometryKollektion
* LineString (Radsträng)
* MultiLineString
* Multipoint
* MultiPolygon (multipolygon)
* Punkt
* Polygon

Vissa Azure Maps API:er accepterar geometrier som inte ingår i [GeoJSON-specifikationen][1]. Api:et [för sök inuti geometri](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) accepterar till exempel Circle och Polygoner.

Den här artikeln innehåller en detaljerad förklaring om hur Azure Maps utökar [GeoJSON-specifikationen][1] för att representera vissa geometrier.

## <a name="circle"></a>Cirkel

Geometrin `Circle` stöds inte av [GeoJSON-specifikationen][1]. Vi använder `GeoJSON Point Feature` ett objekt för att representera en cirkel.

En `Circle` geometri som `GeoJSON Feature` representeras med objektet __måste__ innehålla följande koordinater och egenskaper:

- Centrera

    Cirkelns mittpunkt representeras med `GeoJSON Point` ett objekt.

- Radie

    Cirkelns `radius` representeras med `GeoJSON Feature`hjälp av 's egenskaper. Radievärdet är i _mätare_ och `double`måste vara av typen .

- Subtyp

    Cirkelgeometrin måste `subType` också innehålla egenskapen. Denna egenskap måste vara `GeoJSON Feature`en del av 's egenskaper och dess värde bör _Circle_

#### <a name="example"></a>Exempel

Så här representerar du en cirkel `GeoJSON Feature` med ett objekt. Låt oss centrera cirkeln vid latitud: 47.639754 och longitud: -122.126986, och tilldela den en radie som är lika med 100 meter:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Rektangel

Geometrin `Rectangle` stöds inte av [GeoJSON-specifikationen][1]. Vi använder `GeoJSON Polygon Feature` ett objekt för att representera en rektangel. Rektangeltillägget används främst av Webb-SDK:s ritverktygsmodul.

En `Rectangle` geometri som `GeoJSON Polygon Feature` representeras med objektet __måste__ innehålla följande koordinater och egenskaper:

- Hörn

    Rektangelns hörn representeras med hjälp av `GeoJSON Polygon` ett objekts koordinater. Det bör finnas fem koordinater, en för varje hörn. Och, en femte koordinat som är samma som den första koordinaten, för att stänga polygonringen. Det kommer att antas att dessa koordinater anpassa, och att utvecklaren kan rotera dem som önskat.

- Subtyp

    Rektangelgeometrin måste `subType` också innehålla egenskapen. Den här egenskapen `GeoJSON Feature`måste vara en del av 's egenskaper, och dess värde ska vara _Rectangle_

### <a name="example"></a>Exempel

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Nästa steg

Läs mer om GeoJSON-data i Azure Maps:

> [!div class="nextstepaction"]
> [Geofence GeoJSON-format](geofence-geojson.md)

Granska ordlistan med vanliga tekniska termer som är associerade med Azure Maps och platsinformationsprogram:

> [!div class="nextstepaction"]
> [Azure Maps-ordlista](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
