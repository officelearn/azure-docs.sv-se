---
title: Lägg till ett polygon extrusion-lager till en karta | Microsoft Azure Maps
description: Så här lägger du till ett polygon extrusion-lager i Microsoft Azure Maps-webbsdk.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 36914240caf3c1321dfa0102bd87cb29173f8b1d
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911054"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Lägg till ett polygon extrusion-lager på kartan

Den här artikeln visar hur du använder polygon extrusion-lagret för att återge områden i `Polygon` och `MultiPolygon` funktions Geometries som extruderade former på kartan. Azure Maps Web SDK stöder även skapande av cirkel-Geometries som definierats i det [utökade INTERjson-schemat](extend-geojson.md#circle). Dessa cirklar omvandlas till polygoner när de återges på kartan. Alla funktioner Geometries kan också enkelt uppdateras om de omslutits med [atlasen. Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) -klass.


## <a name="use-a-polygon-extrusion-layer"></a>Använda ett polygon extrusion-lager

När ett [polygon-extrusion-lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) är anslutet till data källan och läses in på kartan, återges områdena i en `Polygon` och `MultiPolygon` funktioner som extruderade former. Egenskaperna `height` och `base` för polygon extrusion-lagret definierar bas avståndet från den djupade formens och höjden i **meter**. Följande kod visar hur du skapar en polygon, lägger till den i en data källa och återger den med hjälp av skikt klassen polygon extrusion.

> [!Note]
> `base` svärdet som definieras i polygon extrusion-lagret ska vara mindre än eller lika med `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extruderad polygon" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>förskjutande polygon</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-multipolygons"></a>Lägg till data drivna multipolygoner

En choropleth-karta kan återges med hjälp av polygon extrusion-lagret genom att ställa in dess `height` och `fillColor` egenskaper i proportion till måttet för den statistiska variabeln i `Polygon` och `MultiPolygon` funktionen Geometries. I följande kod exempel visas en extruderad choropleth karta för U. S baserat på måttet för populationens densitet per tillstånd.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Karta över extruderad choropleth" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/eYYYNox'>Nedskjutande Penn choropleth-kartan</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Lägg till en cirkel till kartan

Azure Maps använder en utökad version av det injson-schema som tillhandahåller en definition för cirklar som anges [här](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). En extruderad cirkel kan återges på kartan genom att skapa en `point`-funktion med en `subType` egenskap för `Circle` och en numrerad `Radius`-egenskap som representerar radien i **meter**. Ett exempel:

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

Azure Maps Web SDK konverterar dessa `Point`s funktioner till `Polygon` funktioner under huven och kan återges på kartan med polygon extrusion-skiktet som visas i följande kod exempel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Drönare luft rummets polygon" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polygon drönare-luft rummets polygon</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Anpassa ett polygon extrusion-lager

Polygonen extrusion skikt flera format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen- <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [polygon extrusion-lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Ytterligare resurser:

> [!div class="nextstepaction"]
> [Tillägg för Azure Mapsal JSON-specifikation](extend-geojson.md#circle)
