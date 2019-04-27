---
title: Lägg till ett popup-fönster med Azure Maps | Microsoft Docs
description: Lägga till ett popup-fönster i Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a6c8a8aa954379036ce566a205b8cb4e97952727
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769557"
---
# <a name="add-a-popup-to-the-map"></a>Lägga till ett popup-fönster på kartan

Den här artikeln visar hur du lägger till ett popup-fönster till en tidpunkt på en karta.

## <a name="understand-the-code"></a>Förstå koden

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Lägga till en pop dig med Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>lägga till en pop dig med Azure Maps</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar. Det skapar också HTML innehåll som ska visas i popup-fönstret.

Andra kodblocket skapar en datakälla objekt med den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En återställningspunkt är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) av den [peka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) klass. Ett point-objekt med ett namn och beskrivning egenskaper sedan skapas och läggs till datakällan.

En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan.  En symbol-lager har skapats i det tredje kodblocket. Datakällan har lagts till symbol-lagret, som sedan läggs till i kartan.

Det fjärde kodblocket skapar en [popup-fönstret objektet](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) via `new atlas.Popup()`. Popup-fönstret Egenskaper som position och pixelOffset är en del av [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions). PopupOptions kan definieras i popup-konstruktorn eller via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funktion i klassen popup-fönstret. En `mouseover` händelselyssnaren för symbolen lager skapas.

Det sista blocket kod skapar en funktion som utlöses av den `mouseover` händelselyssnaren. Den anger innehåll och egenskaper för popup-fönstret och lägger till objektet popup-fönster på kartan.

## <a name="reusing-a-popup-with-multiple-points"></a>Återanvända ett popup-fönster med flera platser

När du har ett stort antal punkter och bara vill visa en popup-fönstret i taget, är det bästa sättet att skapa en popup-fönstret och återanvända den i stället skapa ett popup-fönster för varje punkt-funktion. På så vis, minskar antalet DOM-element som skapas av programmet avsevärt som kan ge bättre prestanda. Det här exemplet skapar 3 punkt funktioner. Om du klickar på någon av dem, visas ett popup-fönster med innehåll för den punkt-funktionen.

<br/>

<iframe height='500' scrolling='no' title='Återanvända popup-fönstret med flera PIN-koder' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>återanvända popup-fönstret med flera PIN-koder</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Finns i följande artiklar som är bra för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Lägg till en symbol-lager](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till en HTML-markör](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Lägga till en form](./map-add-shape.md)