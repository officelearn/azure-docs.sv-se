---
title: Skapa en karta med Azure Maps | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du återger en karta på en webb sida med hjälp av Microsoft Azure Maps-webbsdk.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 49c86f3e6c654ecbfcd07809f42a1b038ca3f8ab
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911112"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln visar hur du skapar en karta och animerar en karta.  

## <a name="loading-a-map"></a>Läser in en karta

Om du vill läsa in en karta skapar du en ny instans av [kart klassen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Vid initieringen av kartan skickas ett DIV-element-ID för att återge kartan och en uppsättning alternativ som ska användas vid inläsning av kartan. Om standard information om autentisering inte anges i `atlas` namn området måste den här informationen anges i kart alternativen vid inläsning av kartan. Kartan läser in flera resurser asynkront för prestanda. När du har skapat kart instansen bifogar du en `ready`-eller `load`-händelse till kartan och lägger sedan till ytterligare kod som samverkar med kartan i den händelse hanteraren. `ready`-händelsen utlöses så snart kartan har tillräckligt med resurser för att kunna interagera med program mässigt. `load` händelse utlöses efter att den inledande kart visningen har lästs in helt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grundläggande kart belastning" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>grundläggande kart inläsningen</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Flera Maps kan läsas in på samma sida och var och en kan använda samma eller olika inställningar för autentisering och språk.

## <a name="show-a-single-copy-of-the-world"></a>Visa en enskild kopia av världen

När kartan zoomas ut på en bred skärm visas flera kopior av världen vågrätt. Detta är bra för de flesta scenarier, men vissa för vissa program kan vara önskvärda att bara se en enda kopia av världen. Detta kan göras genom att ange Maps-`renderWorldCopies` alternativet för att `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = falskt" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen- <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Styra kart kameran

Det finns två sätt att ställa in det visade avsnittet i kartan med kameran. Du kan ställa in kamera alternativ som centrera och zooma när du läser in kartan, eller anropa alternativet `setCamera` när kartan har lästs in för att program mässigt uppdatera Map-vyn.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ange kamera

I följande kod skapas ett [kart objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) och alternativen för centrum och zoomning är inställda. Kart egenskaper som Center och zoomnings nivå är en del av [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>skapa en karta via `CameraOptions` </a>av Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ange kamerans gränser

I följande kod skapas ett [kart objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) via `new atlas.Map()`. Kart egenskaper som `CameraBoundsOptions` kan definieras via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -funktionen för kart klassen. Egenskaper för gränser och utfyllnad anges med `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>skapa en karta via `CameraBoundsOptions` </a>av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animera Map-vy

I följande kod skapar det första kod blocket en karta och anger kopplings formatet, värdena för centrum och zoomning. I det andra kod blocket skapas en klick händelse hanterare för knappen animera. När du klickar på den här knappen anropas funktionen setCamera med vissa slumpmässiga värden för [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animera Map-vy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Map- <a href='https://codepen.io/azuremaps/pen/WayvbO/'>vyn</a> för pen-animering genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Testa koden

Ta en titt på exempel koden ovan. Du kan redigera JavaScript-koden på **fliken JS** till vänster och se ändringarna i Map-vyn på **fliken resultat** till höger. Du kan också klicka på knappen **Redigera på CodePen** och redigera koden i CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Se kod exempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Ändra stil på kartan](choose-map-style.md)

> [!div class="nextstepaction"]
> [Lägg till kontroller till kartan](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)