---
title: Lägga till ett linjelager på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till ett linjelager på en karta med Hjälp av Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933465"
---
# <a name="add-a-line-layer-to-the-map"></a>Lägga till ett linjelager på kartan

Ett linjelager kan användas `LineString` `MultiLineString` för att återge och funktioner som banor eller rutter på kartan. Ett linjelager kan också användas för `Polygon` `MultiPolygon` att återge konturen av och funktioner. En datakälla är ansluten till ett linjelager för att förse den med data att återge. 

> [!TIP]
> Linjelager som standard återger koordinaterna för polygoner samt rader i en datakälla. Om du vill begränsa lagret så att det `filter` bara återger `['==', ['geometry-type'], 'LineString']` LineString-funktioner anger egenskapen för lagret till eller `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` om du också vill inkludera MultiLineString-funktioner.

Följande kod visar hur du skapar en rad. Lägg till raden i en datakälla och återge den sedan med ett linjelager med klassen [LineLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Nedan visas det fullständiga kodexemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en linje på en karta' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Lägg till en rad på en karta</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Linjelager kan formateras med [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) och [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Lägga till symboler längs en linje

Det här exemplet visar hur du lägger till pilikoner längs en linje på kartan. När du använder ett symbollager ställer du in alternativet "placering" på "linje". Det här alternativet återger symbolerna längs linjen och roterar ikonerna (0 grader = höger).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Visa pil längs linje" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/drBJwX/'>pilen</a> Pen Show längs<a href='https://codepen.io/azuremaps'>@azuremaps</a>linjen med Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps web SDK innehåller flera anpassningsbara avbildningsmallar som du kan använda med symbollagret. Mer information finns i dokumentet [Så här använder du bildmallar.](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Lägga till en linjeövertoning på en linje

Du kan använda en enda linjefärg på en linje. Du kan också fylla en linje med en övertoning av färger för att visa övergången från ett linjesegment till nästa radsegment. Linjeövertoningar kan till exempel användas för att representera ändringar över tid och avstånd, eller olika temperaturer över en ansluten linje med objekt. För att kunna använda den här funktionen på `lineMetrics` en rad måste datakällan ha alternativet inställt `strokeColor` på true och sedan kan ett färgtoningsuttryck skickas till alternativet för raden. Penntoninguttrycket måste `['line-progress']` referera till datauttrycket som exponerar de beräknade radmåtten för uttrycket.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linje med linjeövertoning" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>pennlinjen med linjeövertoning</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Anpassa ett linjelager

Linjelagret har flera formateringsalternativ. Här är ett verktyg för att prova dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för linjelager' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>pennlinjelager</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [LineLayer (på andra)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Så här använder du avbildningsmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)
