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
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988591"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln visar hur du skapar en karta och animerar en karta.  

## <a name="loading-a-map"></a>Läser in en karta

Om du vill läsa in en karta skapar du en ny instans av [kart klassen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). När kartan initieras skickar du ett DIV-element-ID för att återge kartan och skicka en uppsättning alternativ som ska användas vid inläsning av kartan. Om standard information om autentisering inte anges i `atlas` namn området måste den här informationen anges i kart alternativen vid inläsning av kartan. Kartan läser in flera resurser asynkront för prestanda. När du har skapat Map-instansen bifogar du en `ready`-eller `load`-händelse till kartan och lägger sedan till ytterligare kod som samverkar med kartan till händelse hanteraren. `ready`-händelsen utlöses så snart kartan har tillräckligt med resurser för att kunna interagera med program mässigt. `load` händelse utlöses efter att den inledande kart visningen har lästs in helt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grundläggande kart belastning" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>grundläggande kart inläsningen</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Du kan läsa in flera kartor på samma sida. Flera kartor på samma sida kan använda samma eller olika inställningar för autentisering och språk.

## <a name="show-a-single-copy-of-the-world"></a>Visa en enskild kopia av världen

När kartan zoomas ut på en bred skärm visas flera kopior av världen vågrätt. Det här alternativet är bra för vissa scenarier, men för andra program är det önskvärt att se en enda kopia av världen. Detta beteende implementeras genom att ställa in Maps-`renderWorldCopies` alternativet att `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = falskt" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen- <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Styra kart kameran

Det finns två sätt att ange det område som visas i kartan med hjälp av kameran på en karta. Du kan ställa in kamera alternativen när du läser in kartan. Du kan också anropa alternativet `setCamera` när kartan har lästs in för att program mässigt uppdatera Map-vyn.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ange kamera

I följande kod skapas ett [kart objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) och alternativen för centrum och zoomning är inställda. Kart egenskaper, till exempel Center och zoomnings nivå, ingår i [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

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

I följande kod skapar det första kod blocket en karta och anger kart formaten Enter och zoom. I det andra kod blocket skapas en klick händelse hanterare för knappen animera. När du klickar på den här knappen anropas funktionen `setCamera` med vissa slumpmässiga värden för [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) och [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animera Map-vy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Map- <a href='https://codepen.io/azuremaps/pen/WayvbO/'>vyn</a> för pen-animering genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Testa koden

Titta på kod exemplen. Du kan redigera JavaScript-koden inuti **fliken JS** och se ändringarna i Map-vyn på **fliken resultat**. Du kan också klicka på **Redigera på CodePen**, i det övre högra hörnet och ändra koden i CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

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
