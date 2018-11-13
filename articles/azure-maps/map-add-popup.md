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
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568122"
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

Det fjärde kodblocket skapar en [popup-fönstret objektet](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) via `new atlas.Popup()`. Popup-fönstret Egenskaper som position och pixelOffset är en del av [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions kan definieras i popup-konstruktorn eller via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funktion i klassen popup-fönstret. En `mouseover` händelselyssnaren för symbolen lager skapas.

Det sista blocket kod skapar en funktion som utlöses av den `mouseover` händelselyssnaren. Den anger innehåll och egenskaper för popup-fönstret och lägger till objektet popup-fönster på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Finns i följande artiklar som är bra för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Lägga till en form](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till anpassade HTML](./map-add-custom-html.md)
