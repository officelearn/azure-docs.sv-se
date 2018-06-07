---
title: Skapa en karta med Azure Maps | Microsoft Docs
description: Så här skapar du en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ec85854e5d9b7ee0d5e2c54881a417ba6cbb366e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599821"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln visar hur du skapar en karta.  

## <a name="understand-the-code"></a>Förstå koden

Det finns två sätt som du kan skapa en karta. Du kan ange kameran för kartan genom att ange mittpunkt och zoomnivå eller ange kamera gränserna för kartan southwest avgränsar punkt och nordöst avgränsar punkt.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Ange kameran

<iframe height='310' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>skapa en karta via CameraOptions</a> av Azure kg (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan, en [kartan objektet](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) som skapas via `new atlas.Map()`. Egenskaper för till exempel Centrera och zooma nivån är en del av [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest). CameraOptions kan definieras i kartan konstruktor eller via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) funktion i klassen kartan.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Ange gränser för kamera

<iframe height='310' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>skapa en karta via CameraBoundsOptions</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan, en [kartan objektet](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) har skapats `new atlas.Map()`. Egenskaper för till exempel angränsande ruta är en del av [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions kan definieras [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) funktion i klassen kartan.

## <a name="try-out-the-code"></a>Prova att använda koden 

Ta en titt på exempelkoden ovan. Du kan redigera JavaScript-kod på fliken JS till vänster och se kartan visa ändringar på fliken resultat till höger. Du kan också klicka på knappen ”Redigera på CodePen” och redigera koden i CodePen. 

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [karta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    
Att lägga till i din maps mer kodexempel finns i följande artiklar: 
* [Lägga till en PIN-kod](./map-add-pin.md)
* [Lägg till ett popup-fönster](map-add-popup.md)
    

