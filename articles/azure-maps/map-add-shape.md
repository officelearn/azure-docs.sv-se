---
title: Lägg till ett polygon-skikt i Azure Maps | Microsoft Docs
description: Så här lägger du till ett polygon-lager i Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976145"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Lägg till ett polygon-lager till kartan

Den här artikeln visar hur du återger områdena i `Polygon` och `MultiPolygon` funktions Geometries på kartan med ett polygon-lager. Azure Maps Web SDK stöder även skapande av cirkel-Geometries som definierats i det [utökade INTERjson-schemat](extend-geojson.md#circle). Dessa cirklar omvandlas till polygoner när de återges på kartan. Alla funktioner Geometries kan också enkelt uppdateras om de omslutits med [atlasen. Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) -klass.

## <a name="use-a-polygon-layer"></a>Använda ett polygon-lager 

När ett polygon-lager är anslutet till en data källa och läses in på kartan, återges delen av en `Polygon` - `MultiPolygon` och-funktioner. Följande kod visar hur du skapar en polygon, lägger till den i en data källa och återger den med ett polygon-lager med hjälp av klassen [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) .

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägg till en polygon till en karta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Lägg till en polygon till en karta</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Använda ett polygon-och linje lager tillsammans

Ett linje lager kan användas för att återge konturen för polygoner. I följande kod exempel återges en polygon som i föregående exempel, men nu läggs ett linje lager till som ett andra lager som är anslutet till data källan.  

<iframe height='500' scrolling='no' title='Polygon och linje skikt för att lägga till polygon' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Lägg till polygon</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>i Pen-polygonen och linje lagret.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Fylla en polygon med ett mönster

Förutom att fylla en polygon med en färg kan du också använda ett bild mönster. Läs in ett bild mönster i Maps-avbildningen Sprite-resurser och referera sedan `fillPattern` till den här avbildningen med egenskapen för polygon-lagret.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fyllnings mönster för polygon" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se fyllnings <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>mönstret</a> för pen-polygonen<a href='https://codepen.io/azuremaps'>@azuremaps</a>genom att Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Azure Maps Web SDK innehåller flera anpassningsbara bildmallar som du kan använda som fyllnings mönster. Mer information finns i dokumentet om [att använda](how-to-use-image-templates-web-sdk.md) bildmallar.

## <a name="customize-a-polygon-layer"></a>Anpassa ett polygon-lager

Polygon-lagret har bara några få format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Lägg till en cirkel till kartan

Azure Maps använder en utökad version av det injson-schema som tillhandahåller en definition för cirklar som anges [här](extend-geojson.md#circle). En cirkel kan återges på kartan genom `Point` att skapa en funktion som har en `subType` `"Circle"` egenskap med värdet och en `radius` egenskap som har ett tal som representerar radien i meter. Exempel:

```javascript
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

Azure Maps Web SDK konverterar dessa `Point` funktioner till `Polygon` funktioner under försättsblad och kan återges på kartan med hjälp av polygon-och linje lager som visas i följande kod exempel.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en cirkel till en karta' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Lägg till en cirkel till en karta</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Gör en geometri lätt att uppdatera

En `Shape` klass radbryter en [geometri](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) eller [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) och gör det enkelt att uppdatera och underhålla dem. En form kan skapas genom att skicka i en geometri och en uppsättning egenskaper, eller genom att skicka in en funktion som visas i följande kod.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Följande kod exempel visar hur du omsluter ett cirkel-interjson-objekt med en Shape-klass och enkelt uppdaterar dess RADIUS-egenskap med ett skjutreglage. När RADIUS-värdet ändras i formen uppdateras åter givningen av cirkeln automatiskt på kartan.

<br/>

<iframe height='500' scrolling='no' title='Uppdatera form egenskaper' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se egenskaperna för Penn <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>uppdaterings formen</a> genom Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Fler kod exempel som kan läggas till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Skapa en data Källa](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till en popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Använda bildmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett linje lager](map-add-line-layer.md)

Ytterligare resurser:

> [!div class="nextstepaction"]
> [Tillägg för Azure Mapsal JSON-specifikation](extend-geojson.md#circle)