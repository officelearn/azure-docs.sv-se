---
title: Lägga till ett lager för symbolen i Azure Maps | Microsoft Docs
description: Hur du lägger till symboler på kartan för Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3225ae919e221935b6d8a52e20d943d2178f6a47
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056861"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Lägg till ett lager för symbolen på en karta

Den här artikeln visar hur du kan rendera punkt data från en datakälla som ett lager för symbolen på en karta. Symbol lager återges med WebGL och stöd för mycket större mängder punkter än HTML-markeringar, men stöder inte traditionella CSS och HTML-element för formatering.  

> [!TIP]
> Symbol lager som standard renderas koordinaterna för alla geometrier i en datakälla. Funktioner för att begränsa lagret så att den återger endast punkt geometri set den `filter` tillhör skiktet till `['==', ['geometry-type'], 'Point']` eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du vill inkludera MultiPoint funktioner också.

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

<iframe height='500' scrolling='no' title='Plats för växeln PIN-kod' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>växel PIN-kod plats</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första blocket av koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En [funktion] som innehåller en [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) geometri är omslutna av den [form](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) klassen för att göra det lättare att uppdatera, och sedan skapas och läggs till datakällan.

Det tredje kodblocket skapar en [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) och uppdateringar punktkoordinater vid mus klickar du på med hjälp av klassen shape [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metod.

En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan.  Datakällan och händelselyssnaren klickar du på symbolen lagret skapas och läggs till kartan inom den `ready` [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion så visas punkten efter kartan inläst och klar att användas.

> [!TIP]
> Som standard för prestanda, optimera symbolen lager återgivningen av symboler genom att dölja symboler som överlappar. När du zoomar in dolda symbolerna bli då synligt. Om du vill inaktivera den här funktionen och rendera alla symboler hela tiden, ange den `allowOverlap` egenskapen för den `iconOptions` alternativ `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Lägga till en anpassad ikon i en symbol-lager

Symbol lager återges med WebGL. Som sådana alla resurser, till exempel ikonen avbildningar måste läsas in WebGL kontexten. Detta exempel visar hur du lägger till en anpassad ikon i kartan resurser och sedan använda den för att rendera punkt data med en anpassad symbol på kartan. Den `textField` egenskapen skiktets symbolen kräver ett uttryck som anges. I det här fallet vi vill rendera egenskapen temperatur men eftersom det är ett tal, den behöver konverteras till en sträng. Dessutom vill vi lägga till ”° F”. Ett uttryck kan användas för att göra detta. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikonen för anpassad Symbol-bild' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>anpassad Symbol bild ikonen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Anpassa ett symbol-lager 

Symbol-lagret har många formatalternativ. Här är ett verktyg för att testa de olika formatalternativ.

<br/>

<iframe height='700' scrolling='no' title='Symbol-alternativ för Bildrutsskikt' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PxVXje/'>symbolen alternativ för Bildrutsskikt</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Se följande artiklar om fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Lägg till en form](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](./map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Add HTML Makers](./map-add-bubble-layer.md)
