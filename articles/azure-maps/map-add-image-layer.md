---
title: Lägg till ett bild lager i Azure Maps | Microsoft Docs
description: Så här lägger du till ett bild lager i JavaScript-kartan
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ea0f37e307196af4b27fd3f8fb1aa0d42443dfa
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638731"
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

## <a name="add-an-image-layer"></a>Lägga till ett avbildningsskikt

Det här exemplet visar hur du lägger till en bild av en [karta över Newark New Jersey från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan.

<br/>

<iframe height='500' scrolling='no' title='Enkelt bild lager' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se det <a href='https://codepen.io/azuremaps/pen/eQodRo/'>enkla bild skiktet</a> för pennan genom<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar det första blocket kod ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

I det andra kod blocket skapas en [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) genom att skicka en URL till en bild och koordinera de fyra hörnen i formatet `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Importera ett KMLt bas överlägg

Det här exemplet visar hur du lägger till KML-information för mark överlägg som ett bild lager på kartan. KML-överlägg ger norr, södra, öst-och väst-koordinater och en rotations medsols rotation, där bild lagret förväntar sig koordinater för varje hörn i bilden. KML-markplanet i det här exemplet är av Chartres-Cathedral och källan från [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='KML-överlägg som bild skikt' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se KML för Penn <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>överlägg som bild lager</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan använder den statiska `getCoordinatesFromEdges` funktionen i [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) -klassen för att beräkna de fyra hörnen av bilden från norra, södra, östra, västra och rotations information från KML-markplanet.


## <a name="customize-an-image-layer"></a>Anpassa ett bild lager

Bild lagret har många format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för bild lager' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>alternativen</a> för Penn bildskiktet genom Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
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