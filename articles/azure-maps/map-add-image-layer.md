---
title: Lägg till ett bild lager i Azure Maps | Microsoft Docs
description: Så här lägger du till ett bild lager i Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fadaaf7c64b11a6d6d94c68234f8288d1b3f8d07
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480498"
---
# <a name="add-an-image-layer-to-a-map"></a>Lägga till ett bild lager till en karta

Den här artikeln visar hur du kan täcka en bild till en fast uppsättning koordinater på kartan. Det finns många scenarier där det är möjligt att täcka en bild på kartan. Här är några exempel på den typ av bilder som ofta finns på kartor.

* Bilder som samlats in från drönare.
* Skapar Floorplans.
* Historiska eller andra specialiserade kart bilder.
* Ritningar av jobb webbplatser.
* Väder radar bilder.

> [!TIP]
> En [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) är ett snabbt sätt att täcka en bild på en karta. Men om bilden är stor kan det vara svårt att läsa in den i webbläsaren. I det här fallet bör du dela upp bilden i panelerna och läsa in dem i kartan som en [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Bild lagret stöder följande bild format:

- -
- KÄLLFIL
- BITMAPPSBILD
- GIF (inga animeringar)

## <a name="add-an-image-layer"></a>Lägga till ett avbildningsskikt

I följande kod överlappar en bild av en [karta över Newark New Jersey från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan. En [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) skapas genom att skicka en URL till en bild och koordinater för de fyra hörnen i formatet `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Enkelt bild lager' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se det <a href='https://codepen.io/azuremaps/pen/eQodRo/'>enkla bild skiktet</a> för pennan genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Importera ett KMLt bas överlägg

Det här exemplet visar hur du lägger till KML-information för mark överlägg som ett bild lager på kartan. KML-överlägg ger norr, syd, öst och västra koordinater och en rotations medsols, medan bild lagret förväntar sig koordinater för varje hörn i bilden. KML-markplanet i det här exemplet är av Chartres-Cathedral och källan från [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

I följande kod används den statiska `getCoordinatesFromEdges` funktionen i [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) -klassen för att beräkna de fyra hörnen av bilden från norra, södra, östra, västra och rotations information från KML-markplanet.

<br/>

<iframe height='500' scrolling='no' title='KML-överlägg som bild skikt' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se KML för Penn <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>överlägg som bild lager</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Anpassa ett bild lager

Bild lagret har många format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för bild lager' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för Penn <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Bildskiktet</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till ett panel lager](./map-add-tile-layer.md)