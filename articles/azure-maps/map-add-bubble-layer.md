---
title: Lägga till ett lager för bubbla i Azure Maps | Microsoft Docs
description: Hur du lägger till ett bubbeldiagram lager till Javascript-karta
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 895f6ce728ce608184bf6f68be3b73d5dc384d79
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892516"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Lägg till ett bubbeldiagram lager till en karta

Den här artikeln visar hur du kan rendera punkt data från en datakälla som ett bubbeldiagram lager på en karta. Bubbeldiagram lager renderas punkter som cirklar på kartan med fast pixel radius. 

> [!TIP]
> Bubbeldiagram lager som standard renderas koordinaterna för alla geometrier i en datakälla. Funktioner för att begränsa lagret så att den återger endast punkt geometri set den `filter` tillhör skiktet till `['==', '$type', 'Point']`

## <a name="add-a-bubble-layer"></a>Lägg till ett bubbeldiagram lager

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, en matris med [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objekt definieras och läggs till i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objekt.

En [bubbla layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) återger platsbaserad data och är inneslutna i den [datakälla](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som cirklar på kartan. Det sista blocket kod skapar ett bubbeldiagram lager och lägger till den på kartan. Se egenskaperna för en bubbla lager på [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Punkt objektmatris datakällan och bubbeldiagram lagret skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att cirkeln visas efter kartan har lästs in helt.

## <a name="show-labels-with-a-bubble-layer"></a>Visa etiketter med ett bubbeldiagram lager

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan visar hur du visualisera och Märk data på kartan. Det första blocket av koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Det andra kodblocket, skapar en [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objekt. Det skapar sedan en datakälla objekt med den [datakälla](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klassen och lägger till punkten till datakällan.

En [bubbla layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) återger platsbaserad data och är inneslutna i den [datakälla](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som cirklar på kartan. Det tredje kodblocket skapas ett bubbeldiagram lager och lägger till den på kartan. Se egenskaperna för en bubbla lager på [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. Senaste kodblocket skapar och lägger till en symbol-lager för kartan som återger textetiketten för bubblan. Se egenskaperna för en symbol-lager i [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Datakällan och lagren skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att data visas när kartan har lästs in helt.

## <a name="customize-a-bubble-layer"></a>Anpassa ett bubbeldiagram lager

Bubbeldiagram-lager har endast några alternativ för formatering. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Bubbeldiagram alternativ för Bildrutsskikt' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>visas alternativ för Bildrutsskikt</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar om fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till en symbol-lager](./map-add-pin.md)