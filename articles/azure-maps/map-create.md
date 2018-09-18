---
title: Skapa en karta med Azure Maps | Microsoft Docs
description: Så här skapar du en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730017"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln visar hur du skapar en karta.  

## <a name="understand-the-code"></a>Förstå koden

Det finns två sätt som du kan skapa en karta. Du kan ange kameran på kartan genom att ange mittpunkt och zoomnivå. Ange kamera gränser på kartan genom att ange sydväst avgränsar punkt och nordöst avgränsar punkt.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ange kameran

<iframe height='310' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>skapa en karta via `CameraOptions` </a>av Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan, en [Kartobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) har skapats `new atlas.Map()`. Egenskaper för kartan, till exempel centrering och zoomning nivå är en del av [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` Du kan definiera i konstruktorn kartan eller via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funktion i klassen kartan.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ange gränser för kamera

<iframe height='310' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>skapa en karta via `CameraBoundsOptions` </a>genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan, en [Kartobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) skapas `new atlas.Map()`. Egenskaper för kartan, till exempel angränsande ruta är en del av [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). `CameraBoundsOptions` kan definieras [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funktion i klassen kartan.

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
