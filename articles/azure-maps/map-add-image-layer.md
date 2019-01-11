---
title: Lägga till ett lager i avbildningen i Azure Maps | Microsoft Docs
description: Hur du lägger till ett lager i avbildningen till Javascript-karta
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8d0ad34496963c32c842033e81bdffb375a27412
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214682"
---
# <a name="add-an-image-layer-to-a-map"></a>Lägg till ett bildlager på en karta

Den här artikeln visar hur du kan överlappar en avbildning till en fast uppsättning koordinater på kartan. Det finns många scenarier som överlägg till en bild på kartan är klar. Här följer några exempel på vilken typ av avbildningar som oftast högst upp på kartor;

* Bilder från drönare.
* Skapa floorplans.
* Historik eller andra särskilda kartbilder.
* Skisser för arbetsplatser.
* Väder polärdiagram avbildningar.

> [!TIP]
> En [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) är ett snabbt ett enkelt sätt att täcka över en bild på en karta. Men om avbildningen är stor webbläsaren kan behöva kämpa för att läsa in den. I det här fallet, Överväg att dela din avbildning upp till paneler och läses in i kartan som en [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

## <a name="add-an-image-layer"></a>Lägga till ett avbildningsskikt

Det här exemplet visar hur du överlappar en avbildning av en [mappning av Newark New Jersey från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan.

<br/>

<iframe height='500' scrolling='no' title='Enkel avbildning lager' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/eQodRo/'>enkel bild Layer</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, en [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) har skapats genom att skicka en URL till en bild och koordinaterna för de fyra hörnen i formatet `[Top Left Corner, Top Right Corner, Bottom Right Conter, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Importera ett KML grunden överlägg

Det här exemplet visar hur du företagsdataskydd KML grunden överlägget information som en bildlager på kartan. KML grunden överlägg innehåller Nord, Syd, östra, västra koordinater och en motsols rotation, där som bilden layer förväntar sig koordinater för varje hörn. KML grunden överlägg i det här exemplet är av Chartres cathedral och källkod från [vinstdrivande Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='KML grunden överlägg som bildlager' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML början överlägg som bildlager</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan använder statiskhet `getCoordinatesFromEdges` funktion av den [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) klass för att beräkna hörnen på avbildningen från norr, södra, östra, västra och rotation information från KML början överlägg.


## <a name="customize-an-image-layer"></a>Anpassa ett lager för avbildning

Bild-lagret har många alternativ för formatering. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Layer-Avbildningsalternativen' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Layer alternativ</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar om fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till en bildrutsskikt](./map-add-tile-layer.md)