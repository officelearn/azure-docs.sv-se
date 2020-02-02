---
title: Lägga till kontroller till en karta | Microsoft Azure Maps
description: Så här lägger du till zoomnings kontroll, vinkel kontroll, rotera kontroll och en stil väljare till en karta i Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e2ad8eb181685d3ac3de8b1e0ed7ef8ddfa4e224
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933287"
---
# <a name="add-controls-to-a-map"></a>Lägga till kontroller till en karta

Den här artikeln visar hur du lägger till kontroller till en karta. Du lär dig också hur du skapar en karta med alla kontroller och en [format väljare](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Lägg till zoomkontrollen

En zoomnings kontroll lägger till knappar för att zooma in och ut kartan. Följande kod exempel skapar en instans av klassen [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) och lägger till det nedersta högra hörnet på kartan.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en zoomnings kontroll' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>att lägga till en zoomnings kontroll</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Lägg till vinkel kontroll

En lutnings kontroll lägger till knappar för att luta bredden för att mappa i förhållande till horisonten. Följande kod exempel skapar en instans av klassen [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) . Den lägger till PitchControl i det övre högra hörnet på kartan.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en lutnings kontroll' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>att lägga till en kanna-kontroll</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Lägg till kompass kontroll

En kompass-kontroll lägger till en knapp för att rotera kartan. Följande kod exempel skapar en instans av kontroll klassen [kompass](/javascript/api/azure-maps-control/atlas.control.compasscontrol) och lägger till den i det nedre vänstra hörnet på kartan.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en rotations kontroll' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>att lägga till en rotations kontroll</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>En karta med alla kontroller

Följande kod exempel lägger till stil väljarna, zoom, färgdjup och kompass-kontrollerna i det nedre högra hörnet på kartan. Lägg märke till hur de staplas automatiskt. Ordningen på kontroll objekt i skriptet avgör i vilken ordning de visas på kartan. Om du vill ändra ordningen på kontrollerna på kartan kan du ändra deras ordning i skriptet.

<br/>

<iframe height='500' scrolling='no' title='En karta med alla kontroller' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>en karta med alla kontroller</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Format väljar kontrollen definieras av [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) -klassen. Mer information om hur du använder format väljar kontrollen finns i [Välj ett kart format](choose-map-style.md).

## <a name="customize-controls"></a>Anpassa kontroller

Här är ett verktyg för att testa de olika alternativen för att anpassa kontrollerna.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Navigerings kontroll alternativ" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se alternativ för Penn <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>navigerings kontroll</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Om du vill skapa anpassade navigerings kontroller skapar du en klass som sträcker sig från `atlas.Control`-klassen eller skapar ett HTML-element och placerar det ovanför kart div. Använd den här GRÄNSSNITTs kontrollen anropa Maps-`setCamera`-funktionen för att flytta kartan. 

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Kompass kontroll](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

I följande artiklar finns fullständig kod:

> [!div class="nextstepaction"]
> [Lägg till en PIN-kod](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till en popup](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Lägg till ett linje lager](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygon-lager](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till ett bubbel-lager](map-add-bubble-layer.md)

