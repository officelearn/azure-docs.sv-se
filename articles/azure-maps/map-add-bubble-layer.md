---
title: Lägg till ett bubbel-lager till en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till ett bubbel-lager till en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0088cced84da08828d02d3a0f83846babf286b71
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911290"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Lägg till ett bubbel-lager till en karta

Den här artikeln visar hur du kan återge punkt data från en data källa som ett bubbel lager på en karta. Bubbeldiagram återger punkter som cirklar på kartan med en fast pixel-radie. 

> [!TIP]
> Bubbel lager som standard återger koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner ställer du in `filter` egenskapen för lagret på `['==', ['geometry-type'], 'Point']` eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du även vill inkludera MultiPoint-funktioner.

## <a name="add-a-bubble-layer"></a>Lägga till ett bubbelskikt

Följande kod läser in en matris med punkter i en data källa och ansluter den till ett [bubbel lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest). Bubble-lagret har alternativ för att återge radien för varje bubbla vid fem bild punkter, en fyllnings färg med vitt, en linje färg för blått och linje bredden på sex bild punkter. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer data Källa' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>data källan</a> för pen BubbleLayer genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Visa etiketter med ett bubbel-lager

Följande kod visar hur du använder ett bubbeldiagram för att återge en punkt på kartan och ett symbol lager som återger en etikett. Om du vill dölja symbolen för symbol lagret ställer du in egenskapen `image` för de ikon alternativ som du vill `'none'`.

<br/>

<iframe height='500' scrolling='no' title='MultiLayer data Källa' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>data källan</a> för pen MultiLayer genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Anpassa ett bubbel-lager

Bubble-lagret har bara några format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för bubbel Layer' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för rit <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>skiktet</a> ritstift efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en data Källa](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett symbol lager](map-add-pin.md)

> [!div class="nextstepaction"]
> [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)