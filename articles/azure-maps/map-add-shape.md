---
title: Lägga till ett polygonlager på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du renderar en polygon och polygon på en karta i Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535060"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Lägga till ett polygonlager på kartan

Den här artikeln visar hur `Polygon` du `MultiPolygon` återger områden och funktionsgeometrier på kartan med hjälp av ett polygonlager. Azure Maps Web SDK stöder också skapandet av Circle-geometrier enligt definitionen i det [utökade GeoJSON-schemat](extend-geojson.md#circle). Dessa cirklar omvandlas till polygoner när de återges på kartan. Alla funktionsgeometrier kan enkelt uppdateras när de slås in med [atlasen. Formklass.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-layer"></a>Använda ett polygonlager 

När ett polygonlager är anslutet till en datakälla och läses in på kartan återges området med `Polygon` och `MultiPolygon` funktioner. Om du vill skapa en polygon lägger du till den i en datakälla och återger den med ett polygonlager med klassen [PolygonLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

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

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Nedan är det fullständiga och löpande exemplet på ovanstående kod.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en polygon på en karta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pennan <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Lägg till en polygon på en karta</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Använda ett polygon- och linjelager tillsammans

Ett linjelager används för att återge dispositionen av polygoner. Följande kodexempel återger en polygon som föregående exempel, men lägger nu till ett linjelager. Det här linjelagret är ett andra lager som är kopplat till datakällan.  

<iframe height='500' scrolling='no' title='Polygon- och linjelager för att lägga till polygon' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon och linjelagret för att lägga till polygon</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Fyll en polygon med ett mönster

Förutom att fylla en polygon med en färg kan du använda ett bildmönster för att fylla polygonen. Läs in ett bildmönster i kartornas bilder och `fillPattern` referera sedan till den här bilden med polygonlagrets egenskap.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polygonfyllningsmönster" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se fyllningsmönstret för Pen <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Azure Maps web SDK innehåller flera anpassningsbara avbildningsmallar som du kan använda som fyllningsmönster. Mer information finns i dokumentet [Så här använder du bildmallar.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>Anpassa ett polygonlager

Polygonlagret har bara några stylingalternativ. Här är ett verktyg för att prova dem.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg (0, 2000)' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Lägga till en cirkel på kartan

Azure Maps använder en utökad version av GeoJSON-schemat som ger en definition för cirklar, vilket anges [här](extend-geojson.md#circle). En cirkel återges på kartan `Point` genom att skapa en funktion. Detta `Point` har `subType` en egenskap `"Circle"` med `radius` värdet och en egenskap med ett tal som representerar radien i meter. 

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

Azure Maps Web SDK `Point` konverterar `Polygon` dessa funktioner till funktioner. Sedan återges dessa funktioner på kartan med polygon- och linjelager som visas i följande kodexempel.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en cirkel på en karta' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pennan <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Lägg till en cirkel på en karta</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Gör en geometri enkel att uppdatera

En `Shape` klass sveper in en [geometri](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) eller [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) och gör det enkelt att uppdatera och underhålla dessa funktioner. Om du vill instansiera en formvariabel skickar du en geometri eller en uppsättning egenskaper till formkonstruktorn.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Följande kodexempel visar hur du radbryt ett geojson-objekt med en formklass. När värdet på radien ändras i formen återges cirkeln automatiskt på kartan.

<br/>

<iframe height='500' scrolling='no' title='Uppdatera formegenskaper' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>formegenskaperna</a> Pen Update<a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer (på samma sätt)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Fler kodexempel att lägga till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Så här använder du avbildningsmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

Ytterligare resurser:

> [!div class="nextstepaction"]
> [Tillägg för Azure Maps GeoJSON-specifikation](extend-geojson.md#circle)