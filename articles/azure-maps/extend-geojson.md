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
ms.openlocfilehash: 2cc0e29615ad4fc19040055d847435a9dffa9c95
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655383"
---
# <a name="extending-geojson-geometries"></a>Utöka GeoJSON geometrier

Azure Maps innehåller en lista över kraftfulla API: er för att söka i/längs geografiska platser.
API: erna standardisera [GeoJSON-specifikationen] [ 1] för att representera geografiska funktioner (till exempel: tillstånd gränser, vägar).  

Den [GeoJSON-specifikationen] [ 1] stöder endast följande geometrier:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* punkt
* polygon

Vissa Azure Maps API: er (till exempel: [Sök i geometri](https://docs.microsoft.com/en-us/rest/api/maps/search/postsearchinsidegeometry)) accepterar geometrier som ”cirkel”, som inte är en del av den [GeoJSON-specifikationen][1].

Den här artikeln innehåller en detaljerad förklaring på hur Azure Maps utökar den [GeoJSON-specifikationen] [ 1] som representerar vissa geometrier.

### <a name="circle"></a>cirkel

Den `Circle` geometri stöds inte av den [GeoJSON-specifikationen][1]. Vi använder den `GeoJSON Feature` objekt som representerar en cirkel.

En `Circle` geometri representeras med hjälp av den `GeoJSON Feature` objekt __måste__ innehåller följande:

1. Centrera
   >Den cirkelns mittpunkt representeras med hjälp av en `GeoJSON Point` typen.

2. Radie
   >Cirkelns `radius` använder `GeoJSON Feature`'s egenskaper. Radievärdet har _mätare_ och måste vara av typen `double`.

3. Undertyp
   >Cirkel geometrin måste även innehålla den `subType` egenskapen. Den här egenskapen måste vara en del av den `GeoJSON Feature`'s egenskaper och dess värde bör vara _cirkel_


#### <a name="example"></a>Exempel

Här är hur du får en cirkel centreras (latitud: 47.639754, longitud:-122.126986) med en radius som är lika med 100 mätare, med hjälp av en `GeoJSON Feature` objekt:

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
