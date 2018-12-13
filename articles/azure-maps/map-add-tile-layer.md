---
title: Lägga till en bildrutsskikt i Azure Maps | Microsoft Docs
description: Hur du lägger till en panel skiktet på kartan för Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 57cde41bf65c370f86ebc57a39e917b95721ca81
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892473"
---
# <a name="add-a-tile-layer-to-a-map"></a>Lägg till en bildrutsskikt på en karta

Den här artikeln visar hur du kan överlappar en bildrutsskikt på kartan. Bildrutsskikt kan du lägga avbildningar ovanpå Azure Maps grundläggande karta paneler. Mer information om Azure Maps sida vid sida system finns i den [zoomningsnivåer och rutnät](zoom-levels-and-tile-grid.md) dokumentation.

En panel layer belastning i paneler från en server. Dessa avbildningar kan vara före återges och lagras som andra bilden på en server med en namngivningskonvention som förstår bildrutsskikt eller en dynamisk tjänst som genererar avbildningar direkt. Det finns tre olika panelen service namngivningskonventioner som stöds av Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) klassen; 

* X, Y, Zooma notation - baserat på zoomningsnivån, x är kolumnen och y är radpositionen för panelen i rutnätet för panelen.
* Quadkey notation - kombination x, y, Zooma information i ett enda strängvärde som är en unik identifierare för en panel.
* Angränsande ruta – avgränsar koordinater för avgränsningsfält kan användas för att ange en bild i formatet `{west},{south},{east},{north}` som ofta används av [Web mappning Services (WMS)](http://www.opengeospatial.org/standards/wms).

> [!TIP]
> En [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) är ett bra sätt att visualisera stora datauppsättningar på kartan. Inte bara en bildrutsskikt genereras från en avbildning, men vektordata kan också återges som en bildrutsskikt för. Av rendering vektordata som en panel-lager, behöver bara i kartkontroll att läsa in, vilka kan vara mycket mindre filstorlek än vektordata som de representerar. Den här tekniken används av många som behöver att återge miljontals rader med data på kartan.

Panel-URL som skickades till en bildrutsskikt måste vara en http/https-URL till en TileJSON resurs eller en panel URL: en mall som använder följande parametrar: 

* `{x}` -X-position på panelen. Måste också `{y}` och `{z}`.
* `{y}` -Y position på panelen. Måste också `{x}` och `{z}`.
* `{z}` -Zoomnivå för panelen. Måste också `{x}` och `{y}`.
* `{quadkey}` -Panelen quadkey identifierare baserat på Bing Maps panel system namngivningskonventionen.
* `{bbox-epsg-3857}` -En omgivande box-sträng med formatet `{west},{south},{east},{north}` i EPSG 3857 Spatial Reference System.
* `{subdomain}` – En platshållare där underdomän värdena om anges kommer att läggas till.

## <a name="add-a-tile-layer"></a>Lägg till en bildrutsskikt

 Det här exemplet visar hur du skapar en bildrutsskikt som pekar på en uppsättning paneler som använder x, y, Zooma sida vid sida-system. Källan för den här bildrutsskikt är ett väder polärdiagram överlägg från den [Iowa miljön Mesonet av Iowa State University](http://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Panelen Lager med hjälp av X, Y och Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Bildrutsskikt använder X, Y och Z</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, en [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) har skapats genom att skicka en formaterad URL till en panel-tjänst, sida vid sida-storlek och en opacitet så att de blir delvis transparent. Dessutom när du lägger till bildrutsskikt på kartan, den läggs till under den `labels` layer så att etiketterna fortfarande är väl synliga.

## <a name="customize-a-tile-layer"></a>Anpassa en bildrutsskikt

Panel-lagret har endast en många alternativ för formatering. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för Bildrutsskikt' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>panelen Alternativ för Bildrutsskikt</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar om fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till en bildlager](./map-add-image-layer.md)