---
title: Skapa en karta med Azure Maps | Microsoft Docs
description: Så här skapar du en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 659f6def7e6bb045606b7214a4b8b4cab693117b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248175"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln beskrivs olika sätt att skapa en karta och animera en karta.  

## <a name="understand-the-code"></a>Förstå koden

Det finns två sätt som du kan skapa en karta. Du kan ange kameran på kartan genom att ange mittpunkt zoomningsnivån eller du kan ange kamera gränser på kartan genom att ange sydväst och nordöst avgränsar punkter.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ange kameran

<iframe height='500' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>skapa en karta via `CameraOptions` </a>av Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan, en [Kartobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) har skapats `new atlas.Map()` och ställs centrering och zoomning. Egenskaper för kartan, till exempel centrering och zoomning nivå är en del av [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ange gränser för kamera

<iframe height='500' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>skapa en karta via `CameraBoundsOptions` </a>genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan, en [Kartobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) skapas `new atlas.Map()`. Egenskaper för kartans som `CameraBoundsOptions` kan definieras [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funktion i klassen kartan. Gränser och utfyllnadsegenskaper anges med `setCamera`.

### <a name="animate-map-view"></a>Animera kartvyn

<iframe height='500' scrolling='no' title='Animera kartvyn' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animera kartvyn</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket skapar en [Kartobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) via `new atlas.Map()`. Egenskaper för kartan, till exempel centrering och zoomning nivå är en del av [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` Du kan definiera i konstruktorn kartan eller via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funktion i klassen kartan. Den [mappa style](https://review.docs.microsoft.com/azure/azure-maps/supported-map-styles?branch=pr-en-us-54960) är inställd på `road`.

Andra kodblocket skapar en funktion för animera kartan som animeras ändring i kartvyn genom att definiera [AnimateOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.animationoptions?view=azure-iot-typescript-latest) via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funktion. Funktionen utlöses av knappen animera kartan för att generera en slumpmässig zoomnivå vid varje gång du klickar.

## <a name="try-out-the-code"></a>Prova att använda koden

Ta en titt på exempelkoden ovan. Du kan redigera JavaScript-kod på den **JS fliken** till vänster och se kartvyn ändringar på den **resultatet fliken** till höger. Du kan också klicka på den **Redigera på CodePen** knappen och redigera koden i CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se kodexempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Välj kartan format](choose-map-style.md)

> [!div class="nextstepaction"]
> [Lägg till karta kontroller](map-add-controls.md)
