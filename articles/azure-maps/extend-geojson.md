---
title: Utökat Geometries för interjson i Azure Maps | Microsoft Docs
description: Lär dig hur du utökar Geometries för interjson i Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881987"
---
# <a name="extended-geojson-geometries"></a>Utökat interjson-Geometries

Azure Maps innehåller en lista med kraftfulla API: er för att söka i/på geografiska funktioner.
Dessa API: er standardiseras på geografi [specifikation][1] för att representera de geografiska funktionerna (till exempel: tillstånds gränser, vägar).  

Den [INTERjson][1] -specifikationen stöder endast följande Geometries:

* GeometryCollection
* Lin Est ring
* MultiLineString
* MultiPoint
* MultiPolygon
* Pekaren
* Polygonlasso

Vissa Azure Maps-API: er (till exempel: [Sök i geometrin](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) acceptera Geometries som "Circle", som inte ingår i den interjson- [specifikationen][1].

Den här artikeln innehåller en detaljerad förklaring av hur Azure Maps utökar den interjson- [specifikationen][1] som representerar vissa Geometries.

## <a name="circle"></a>Division

Geometrin stöds inte av den geometriska JSON-specifikationen. [][1] `Circle` Vi använder ett `GeoJSON Point Feature` objekt för att representera en cirkel.

En `Circle` geometri som representeras `GeoJSON Feature` med objektet __måste__ innehålla följande:

- Centrera

    Cirkelns mitt punkt representeras med `GeoJSON Point` ett objekt.

- Radie

    Cirkelns `radius` egenskaper representeras `GeoJSON Feature`med hjälp av egenskaperna. RADIUS-värdet är i _meter_ och måste vara av typen `double`.

- Undertyp

    Geometrifiguren cirkel måste också innehålla `subType` egenskap. Den här egenskapen måste vara en del av `GeoJSON Feature`egenskaperna och dess värde måste vara en _cirkel_

#### <a name="example"></a>Exempel

Så här kommer du att representera en cirkel centrerad vid (latitud: 47,639754, longitud:-122,126986) med en radie som är lika med 100 meter `GeoJSON Feature` , med ett objekt:

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

Geometrin stöds inte av den geometriska JSON-specifikationen. [][1] `Rectangle` Vi använder ett `GeoJSON Polygon Feature` objekt för att representera en rektangel. Tillägget Rectangle används främst av Web SDK: s verktyg för rit verktyg.

En `Rectangle` geometri som representeras `GeoJSON Polygon Feature` med objektet __måste__ innehålla följande:

- Hörn

    Rektangelns hörn visas med koordinaterna för ett `GeoJSON Polygon` objekt. Det måste finnas fem koordinater, en för varje hörn och en femte koordinat som är samma som det första för att stänga polygon-ringen. Koordinaterna kommer att antas vara justerade och roterade enligt utvecklaren.

- Undertyp

    Rektangelns geometri måste också innehålla `subType` egenskapen. Den här egenskapen måste vara en del av `GeoJSON Feature`egenskaperna och dess värde måste vara en _rektangel_

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

Läs mer om data i multijson i Azure Maps:

> [!div class="nextstepaction"]
> [Polystängsel, geografiskt JSON-format](geofence-geojson.md)

Granska ord listan med vanliga tekniska villkor som är associerade med Azure Maps-och plats informations program:

> [!div class="nextstepaction"]
> [Azure Maps ord lista](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
