---
title: Lägg till ett bild lager till en karta | Microsoft Azure Maps
description: Lär dig hur du lägger till avbildningar till en karta. Se hur du använder Azure Maps Web SDK för att anpassa bild lager och överläggs bilder på fasta uppsättningar koordinater.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 9bf34b9abd78b62a5ded16ae41b028bf61a3b51d
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086104"
---
# <a name="add-an-image-layer-to-a-map"></a>Lägga till ett bild lager till en karta

Den här artikeln visar hur du lägger till en bild i en fast uppsättning koordinater. Här följer några exempel på olika typer av avbildningar som kan skrivas över på Maps:

* Avbildningar hämtade från drönare
* Skapa Floorplans
* Historiska eller andra specialiserade kart bilder
* Ritningar av jobb webbplatser
* Väder radar bilder

> [!TIP]
> En [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer) är ett enkelt sätt att täcka över en bild på en karta. Observera att webbläsare kan ha svårt att läsa in en stor bild. I det här fallet kan du dela upp bilden i paneler och läsa in dem i kartan som en [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer).

Bild lagret stöder följande bild format:

- -
- PNG
- BMP
- GIF (inga animeringar)

## <a name="add-an-image-layer"></a>Lägga till ett avbildningsskikt

Följande kod överlappar en bild av en [karta över Newark, New Jersey, från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan. En [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer) skapas genom att en URL skickas till en bild och koordinaterna för de fyra hörnen i formatet `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` .

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

Här är det fullständiga kod exemplet i föregående kod.

<br/>

<iframe height='500' scrolling='no' title='Enkelt bild lager' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se det <a href='https://codepen.io/azuremaps/pen/eQodRo/'>enkla bild skiktet</a> för pennan genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importera en KML-fil som mark överlägg

Det här exemplet visar hur du lägger till KML-information för mark överlägg som ett bild lager på kartan. KML-överlägg ger norra, syd-, sydöstra-och västra koordinater och en rotations medsols rotation. Men bild lagret förväntar sig att koordinaterna för varje hörn i bilden. KML-markplanet i det här exemplet är för Chartres-Cathedral och det kommer från [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Koden använder den statiska `getCoordinatesFromEdges` funktionen från klassen [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer) . Den beräknar bildens fyra hörn med hjälp av Nord-, syd-, sydöstra-, väst-och rotations informationen för KML-markplanet.

<br/>

<iframe height='500' scrolling='no' title='KML-överlägg som bild skikt' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se KML för Penn <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>överlägg som bild lager</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Anpassa ett bild lager

Bild lagret har många format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för bild lager' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för Penn <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Bildskiktet</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](./map-add-tile-layer.md)