---
title: Lägg till en symboler och brytpunkter med Azure Maps | Microsoft Docs
description: Hur du lägger till symboler och markörer till en Javascript-karta
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c56ac35f49c364b7b0f2ad26b82b178411419414
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282693"
---
# <a name="add-symbols-and-markers-to-a-map"></a>Lägg till symboler och markörer på en karta

Den här artikeln visar hur du lägger till symboler och markörer på en karta som använder en datakälla.

## <a name="add-a-symbol-marker"></a>Lägga till en symbol-markör

<iframe height='500' scrolling='no' title='Plats för växeln PIN-kod' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>växel PIN-kod plats</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första blocket av koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En återställningspunkt skapas sedan och lagt till datakällan. En återställningspunkt är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) av [peka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest).

Det tredje kodblocket skapar en [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) och uppdateringar punktkoordinater vid mus klickar du på med hjälp av klassen shape [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) metod.

En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan.  Datakällan och händelselyssnaren klickar du på symbolen lagret skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att punkten visas när kartan har lästs in helt.

## <a name="add-a-custom-symbol"></a>Lägg till en anpassad symbol

<iframe height='500' scrolling='no' title='HTML-datakällan' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra blockeringen av kod lägger till en [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) i kartan med hjälp av den [markörer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) egenskapen för den [kartan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) klass. HtmlMarker läggs till i kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att den visas när kartan har lästs in helt.

## <a name="add-bubble-markers"></a>Lägg till bubbelmarkörer

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, en matris med positioner definieras och en [MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest) objektet skapas. Ett datakällobjekt skapas sedan med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass och MultiPoint objekt läggs till datakällan.

En [bubbla layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) återger platsbaserad data och är inneslutna i den [datakälla](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som cirklar på kartan. Det sista blocket kod skapar ett bubbeldiagram lager och lägger till den på kartan. Se egenskaperna för en bubbla lager på [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

MultiPoint-objekt och datakällan bubbla lagret skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att cirkeln visas när kartan har lästs in helt.

## <a name="add-bubble-markers-with-label"></a>Lägg till bubbelmarkörer med etiketten

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan visar hur du visualisera och Märk data på kartan. Det första blocket av koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Det andra kodblocket, skapar en [peka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objekt. Det skapar sedan en datakälla objekt med den [datakälla](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klassen och lägger till punkten till datakällan.

En [bubbla layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) återger platsbaserad data och är inneslutna i den [datakälla](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som cirklar på kartan. Det tredje kodblocket skapas ett bubbeldiagram lager och lägger till den på kartan. Se egenskaperna för en bubbla lager på [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. Senaste kodblocket skapar och lägger till en symbol-lager för kartan som återger textetiketten för bubblan. Se egenskaperna för en symbol-lager i [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Datakällan och lagren skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att data visas när kartan har lästs in helt.


## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se följande artiklar om fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Lägga till en form](./map-add-shape.md)