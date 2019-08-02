---
title: Skapa en karta med Azure Maps | Microsoft Docs
description: Så här skapar du en JavaScript-mappning
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8a4f67290e93d8b296added9023fe9b6947ba02c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638677"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln visar hur du skapar en karta och animerar en karta.  

## <a name="understand-the-code"></a>Förstå koden

Det finns två sätt att skapa en karta. Du kan ställa in kartan för kartan genom att ange centrum-och zoomnings nivå eller så kan du ställa in kamerans gränser genom att ange sydvästra och nordöstra gräns punkter.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ange kamera

<iframe height='500' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom att <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>skapa en karta `CameraOptions` via </a>Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapas ett [kart objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) via `new atlas.Map()` och mitten och zoomningen anges. Kart egenskaper som Center och zoomnings nivå är en del av [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ange kamerans gränser

<iframe height='500' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom att <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>skapa en karta `CameraBoundsOptions` via </a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapas ett [kart objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) via `new atlas.Map()`. Kart egenskaper som `CameraBoundsOptions` kan definieras via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -funktionen för kart klassen. Egenskaper för gränser och utfyllnad anges med hjälp `setCamera`av.

### <a name="animate-map-view"></a>Animera Map-vy

<iframe height='500' scrolling='no' title='Animera Map-vy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Map- <a href='https://codepen.io/azuremaps/pen/WayvbO/'>vyn</a> för pen-animering efter<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar det första kod blocket ett [kart objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) via `new atlas.Map()`. Kart egenskaper som Center och zoomnings nivå är en del av [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions`kan definieras i Map-konstruktorn eller via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -funktionen för kart klassen. [Kart formatet](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) är inställt `road`på.

Det andra kod blocket skapar en animerad Map-funktion som animerar förändringar i Map-vyn genom att definiera [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -funktionen. Funktionen utlöses av knappen animera kartan för att generera en slumpmässig zoomnings nivå vid varje klick.

## <a name="try-out-the-code"></a>Testa koden

Ta en titt på exempel koden ovan. Du kan redigera JavaScript-koden på **fliken JS** till vänster och se ändringarna i Map-vyn på **fliken resultat** till höger. Du kan också klicka på knappen **Redigera på CodePen** och redigera koden i CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se kod exempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Välj en kart stil](choose-map-style.md)

> [!div class="nextstepaction"]
> [Lägg till kart kontroller](map-add-controls.md)
