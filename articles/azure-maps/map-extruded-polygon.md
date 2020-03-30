---
title: Lägg till ett polygonextruderingslager på en karta | Microsoft Azure Maps
description: Så här lägger du till ett polygonextruderingslager i Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334549"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Lägga till ett polygonextruderingslager på kartan

Den här artikeln visar hur du använder polygonextruderingsskiktet för att återge områden och `Polygon` `MultiPolygon` har geometrier som extruderade former. Azure Maps Web SDK stöder rendering av Circle-geometrier enligt definitionen i det [utökade GeoJSON-schemat](extend-geojson.md#circle). Dessa cirklar kan omvandlas till polygoner när de återges på kartan. Alla funktionsgeometrier kan enkelt uppdateras när de slås in med [atlasen. Formklass.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-extrusion-layer"></a>Använd ett polygonextruderingsskikt

Anslut [polygonextruderingslagret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) till en datakälla. Sedan laddade den på kartan. Polygonextruderingsskiktet återger `Polygon` a-områdena och `MultiPolygon` har som strängpressade former. Polygonextruderingsskiktets `height` och `base` egenskaper definierar basavståndet från marken och höjden på den strängpressade formen i **meter**. Följande kod visar hur du skapar en polygon, lägger till den i en datakälla och återger den med hjälp av klassen Polygon-extruderingslager.

> [!Note]
> Det `base` värde som definieras i polygonextruderingsskiktet bör `height`vara mindre än eller lika med värdet för .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extruderad polygon" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>Extruded polygon</a> från<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Lägga till datadrivna polygoner

En kolropelth karta kan återges med hjälp av polygon extrudering lager. Ställ `height` in `fillColor` extruderingsskiktets och egenskaper på mätningen av den statistiska variabeln i `Polygon` och `MultiPolygon` funktionens geometrier. Följande kodexempel visar en extruderad choropleth-karta över USA baserat på mätningen av befolkningstätheten efter stat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extruderad choropleth karta" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/eYYYNox'>penextruderad choropleth-karta</a> från<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Lägga till en cirkel på kartan

Azure Maps använder en utökad version av GeoJSON-schemat som ger en definition för cirklar som anges [här](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). En strängpressad cirkel kan återges på kartan `point` genom `subType` att `Circle` skapa en `Radius` funktion med en egenskap och en numrerad egenskap som representerar radien i **meter**. Ett exempel:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK `Point` konverterar `Polygon` dessa funktioner till funktioner under huven. Dessa `Point` funktioner kan återges på kartan med hjälp av polygonextruderingsskikt som visas i följande kodexempel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Drone luftrum polygon" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>Drone-luftrummets polygon</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Anpassa ett polygonextruderingslager

Polygon Extruderingsskiktet har flera stylingalternativ. Här är ett verktyg för att prova dem.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ (av poogBRJ)' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> av<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [polygon extrudering lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Ytterligare resurser:

> [!div class="nextstepaction"]
> [Tillägg för Azure Maps GeoJSON-specifikation](extend-geojson.md#circle)
