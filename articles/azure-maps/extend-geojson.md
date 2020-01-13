---
title: Utökat interjson-Geometries | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur Microsoft Azure Maps utökar geografi specifikationen för att representera vissa Geometries.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 75ad83411edfdfe7545e8f80df17fea56e317ee0
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911642"
---
# <a name="extended-geojson-geometries"></a>Utökat interjson-Geometries

Azure Maps innehåller en lista med kraftfulla API: er för att söka i/på geografiska funktioner.
Dessa API: er standardiseras på geografi [specifikation][1] för att representera de geografiska funktionerna (till exempel: tillstånds gränser, vägar).  

Den [INTERjson-specifikationen][1] stöder endast följande Geometries:

* GeometryCollection
* Lin Est ring
* MultiLineString
* MultiPoint
* MultiPolygon
* Pekaren
* Polygonlasso

Vissa Azure Maps-API: er (till exempel [Sök i geometri](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) acceptera Geometries som "Circle", som inte ingår i den inre [JSON-specifikationen][1].

Den här artikeln innehåller en detaljerad förklaring av hur Azure Maps utökar den [INTERjson-specifikationen][1] som representerar vissa Geometries.

## <a name="circle"></a>Cirkel

`Circle` geometrin stöds inte av den geometriska [JSON-specifikationen][1]. Vi använder ett `GeoJSON Point Feature`-objekt för att representera en cirkel.

En `Circle` geometri som representeras med `GeoJSON Feature`-objektet __måste__ innehålla följande:

- Center

    Cirkelns mitt punkt representeras med ett `GeoJSON Point`-objekt.

- Radie

    Cirkelns `radius` representeras med hjälp av `GeoJSON Feature`egenskaper. RADIUS-värdet är i _meter_ och måste vara av typen `double`.

- Undertyp

    Geometrifiguren cirkel måste också innehålla `subType` egenskap. Den här egenskapen måste vara en del av `GeoJSON Feature`ens egenskaper och dess värde måste vara en _cirkel_

#### <a name="example"></a>Exempel

Så här kommer du att representera en cirkel centrerad på (latitud: 47,639754, longitud:-122,126986) med en radie som är lika med 100 meter, med ett `GeoJSON Feature`-objekt:

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

`Rectangle` geometrin stöds inte av den geometriska [JSON-specifikationen][1]. Vi använder ett `GeoJSON Polygon Feature`-objekt för att representera en rektangel. Tillägget Rectangle används främst av Web SDK: s verktyg för rit verktyg.

En `Rectangle` geometri som representeras med `GeoJSON Polygon Feature`-objektet __måste__ innehålla följande:

- Hörn

    Rektangelns hörn visas med koordinaterna för ett `GeoJSON Polygon`-objekt. Det måste finnas fem koordinater, en för varje hörn och en femte koordinat som är samma som det första för att stänga polygon-ringen. Koordinaterna kommer att antas vara justerade och roterade enligt utvecklaren.

- Undertyp

    Rektangelns geometri måste också innehålla egenskapen `subType`. Den här egenskapen måste vara en del av `GeoJSON Feature`ens egenskaper och dess värde måste vara en _rektangel_

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
