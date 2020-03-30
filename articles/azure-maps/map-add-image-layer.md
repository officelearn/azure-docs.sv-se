---
title: Lägga till ett bildlager på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur du överlagrar en avbildning på en karta med Hjälp av Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209723"
---
# <a name="add-an-image-layer-to-a-map"></a>Lägga till ett bildlager på en karta

I den här artikeln visas hur du överlagrar en bild i en fast uppsättning koordinater. Här är några exempel på olika bildtyper som kan läggas över på kartor:

* Bilder tagna från drönare
* Byggnadsvåningsplan
* Historiska eller andra specialiserade kartbilder
* Ritningar av arbetsplatser
* Väderradarbilder

> [!TIP]
> En [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) är ett enkelt sätt att lägga över en bild på en karta. Observera att webbläsare kan ha svårt att läsa in en stor bild. I det här fallet kan du dela upp bilden i paneler och läsa in dem på kartan som en [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Bildlagret stöder följande bildformat:

- Jpeg
- PNG
- BMP
- GIF (inga animeringar)

## <a name="add-an-image-layer"></a>Lägga till ett avbildningsskikt

Följande kod överlägg en bild av en [karta över Newark, New Jersey, från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan. Ett [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) skapas genom att skicka en URL till en bild `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`och koordinater för de fyra hörnen i formatet .

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

Här är det fullständiga kodexemplet för koden som körs.

<br/>

<iframe height='500' scrolling='no' title='Enkelt bildlager' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Pennformatet bildlager</a> av<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importera en KML-fil som marköverlägg

Det här exemplet visar hur du lägger till KML-marköverlagringsinformation som ett bildlager på kartan. KML mark överlägg ger norr, söder, öster och väster koordinater, och en moturs rotation. Men bildlagret förväntar sig koordinater för varje hörn av bilden. KML marken overlay i detta prov är för Chartres katedralen, och det kommer från [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Koden använder den `getCoordinatesFromEdges` statiska funktionen från klassen [ImageLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) Den beräknar de fyra hörnen av bilden med hjälp av norr, söder, öst, väst, och rotation information om KML marken overlay.

<br/>

<iframe height='500' scrolling='no' title='KML Ground Overlay som bildlager' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay som Image Layer</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Anpassa ett bildlager

Bildlagret har många formateringsalternativ. Här är ett verktyg för att prova dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för bildlager' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativ för <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>pennbildlager</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [BildLager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](./map-add-tile-layer.md)