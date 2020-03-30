---
title: Lägga till kontroller på en karta | Microsoft Azure Maps
description: Så här lägger du till zoomkontroll, tonhöjdskontroll, roterar kontroll och en formatväljare på en karta i Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334568"
---
# <a name="add-controls-to-a-map"></a>Lägga till kontroller på en karta

I den här artikeln visas hur du lägger till kontroller på en karta. Du får också lära dig hur du skapar en karta med alla kontroller och en [stilväljare](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Lägg till zoomkontroll

En zoomkontroll lägger till knappar för att zooma in och ut kartan. Följande kodexempel skapar en förekomst av klassen [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) och lägger till det nedre högra hörnet på kartan.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Nedan visas det fullständiga kodexemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en zoomkontroll' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Lägga till en zoomkontroll</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Lägg till tonhöjdskontroll

En tonhöjdskontroll lägger till knappar för att luta tonhöjden till kartan i förhållande till horisonten. I följande kodexempel skapas en förekomst av klassen [PitchControl.](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Den lägger till PitchControl längst upp till höger på kartan.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Nedan visas det fullständiga kodexemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en tonhöjdskontroll' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Lägga till en pitch-kontroll</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Lägg till kompasskontroll

En kompasskontroll lägger till en knapp för att rotera kartan. Följande kodexempel skapar en förekomst av klassen [Compass Control](/javascript/api/azure-maps-control/atlas.control.compasscontrol) och lägger till det nedre vänstra hörnet på kartan.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Nedan visas det fullständiga kodexemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en rotationskontroll' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan Lägga till en rotera<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>kontroll</a> av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>En karta med alla kontroller

Flera kontroller kan placeras i en matris och läggas till på kartan på en gång och placeras i samma område på kartan för att förenkla utvecklingen. Följande lägger till standardnavigeringskontrollerna på kartan med den här metoden.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

Följande kodexempel lägger till kontrollerna för zoom, kompass, tonhöjd och stilväljare i kartans övre högra hörn. Lägg märke till hur de staplas automatiskt. Ordningen på kontrollobjekten i skriptet avgör i vilken ordning de visas på kartan. Om du vill ändra ordningen på kontrollerna på kartan kan du ändra deras ordning i matrisen.

<br/>

<iframe height='500' scrolling='no' title='En karta med alla kontroller' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A-kartan med alla</a> kontroller<a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Formatväljaren styrs av klassen [StyleControl.](/javascript/api/azure-maps-control/atlas.control.stylecontrol) Mer information om hur du använder formatväljaren finns i [välja ett kartformat](choose-map-style.md).

## <a name="customize-controls"></a>Anpassa kontroller

Här är ett verktyg för att testa de olika alternativen för att anpassa kontrollerna.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Alternativ för navigeringskontroll" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>kontrollalternativen för pennnavigering</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Om du vill skapa anpassade navigeringskontroller skapar du `atlas.Control` en klass som sträcker sig från klassen eller skapar ett HTML-element och placerar det ovanför kartdivan. Låt den här gränssnittskontrollen anropa kartfunktionen `setCamera` för att flytta kartan. 

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Kompasskontroll](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl (Tonhöjd)](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl (format)](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl (ZoomControl)](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Se följande artiklar för fullständig kod:

> [!div class="nextstepaction"]
> [Lägg till en nål](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

