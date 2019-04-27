---
title: Utöka GeoJSON geometrier i Azure Maps | Microsoft Docs
description: Lär dig hur du utökar GeoJSON geometrier i Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: be3c31951c4721a861f9239c5220419dec11b6bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799145"
---
# <a name="extending-geojson-geometries"></a>Utöka GeoJSON geometrier

Azure Maps tillhandahåller en lista med kraftfulla API: er för att söka i/längs geografiska platser.
Dessa API: er som standard [GeoJSON-specifikationen] [ 1] för att representera geografiska funktioner (till exempel: tillstånd gränser, vägar).  

Den [GeoJSON-specifikationen] [ 1] stöder endast följande geometrier:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Återställningspunkt
* Polygon

Vissa API: er för Azure Maps (till exempel: [Sök i geometri](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) accepterar geometrier som ”cirkel”, som inte är en del av den [GeoJSON-specifikationen][1].

Den här artikeln innehåller en detaljerad förklaring på hur Azure Maps utökar den [GeoJSON-specifikationen] [ 1] som motsvarar vissa geometrier.

### <a name="circle"></a>Cirkel

Den `Circle` geometri stöds inte av den [GeoJSON-specifikationen][1]. Vi använder den `GeoJSON Feature` objekt som representerar en cirkel.

En `Circle` geometri representeras med hjälp av den `GeoJSON Feature` objekt __måste__ innehåller följande:

1. Centrera
   >Cirkelns center visas med en `GeoJSON Point` typen.

2. Radie
   >Cirkelns `radius` visas med `GeoJSON Feature`'s egenskaper. Radius-värdet är i _taxor_ och måste vara av typen `double`.

3. Undertyp
   >Geometrifiguren cirkel måste också innehålla `subType` egenskap. Den här egenskapen måste vara en del av `GeoJSON Feature` egenskaper och dess värde bör vara _Cirkel_


#### <a name="example"></a>Exempel

Här är hur du kommer representerar en cirkel centreras (latitud: 47.639754, longitud:-122.126986) med en radius som är lika med 100 taxor, med hjälp av en `GeoJSON Feature` objekt:

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

[1]: https://tools.ietf.org/html/rfc7946
