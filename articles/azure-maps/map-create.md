---
title: Skapa en karta med Azure Maps | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du återger en karta på en webbsida med hjälp av Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfeff430e5313c8728582c4790c9aca9482d63aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534925"
---
# <a name="create-a-map"></a>Skapa en karta

Den här artikeln visar olika sätt att skapa en karta och animera en karta.  

## <a name="loading-a-map"></a>Läsa in en karta

Om du vill läsa in en karta skapar du en ny förekomst av [klassen Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). När du initierar kartan skickar du ett DIV-element-ID för att återge kartan och skicka en uppsättning alternativ som ska användas när kartan läses in. Om standardautentiseringsinformation inte anges på `atlas` namnområdet måste den här informationen anges i kartalternativen när kartan läses in. Kartan läser in flera resurser asynkront för prestanda. När du har skapat kartförekomsten `ready` `load` bifogar du en händelse på kartan och lägger sedan till ytterligare kod som interagerar med kartan i händelsehanteraren. Händelsen `ready` utlöses så fort kartan har tillräckligt med resurser som laddas för att interagera med programmässigt. Händelsen `load` utlöses när den första kartvyn har lästs in helt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grundläggande kartbelastning" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Kartbelastningen</a> för Penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>Grundläggande av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Du kan läsa in flera kartor på samma sida. Flera karta på samma sida kan använda samma eller olika autentiserings- och språkinställningar.

## <a name="show-a-single-copy-of-the-world"></a>Visa en enda kopia av världen

När kartan zoomas ut på en bred skärm visas flera kopior av världen vågrätt. Det här alternativet är bra för vissa scenarier, men för andra program är det önskvärt att se en enda kopia av världen. Det här beteendet implementeras `renderWorldCopies` genom `false`att alternativet kartor ställs in på .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = falskt" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Kartalternativ

När du skapar en karta där, finns flera olika typer av alternativ som kan skickas in för att anpassa hur kartan fungerar som anges nedan.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) och [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) används för att ange vilket område kartan ska visa.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) används för att ange hur kartan ska interagera med tjänster som driver kartan.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) används för att ange att kartan ska formateras och återges.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) används för att ange hur kartan ska nå när användaren interagerar med kartan. 

Dessa alternativ kan också uppdateras när kartan `setCamera` `setServiceOptions`har `setStyle`lästs in med hjälp av funktionerna , , och `setUserInteraction` funktionerna. 

## <a name="controlling-the-map-camera"></a>Styra kartkameran

Det finns två sätt att ställa in kartans yta med hjälp av en kartas kamera. Du kan ställa in kameraalternativen när du läser in kartan. Du kan också `setCamera` ringa alternativet när som helst efter att kartan har lästs in för att programmässigt uppdatera kartvyn.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ställ in kameran

Kartkameran styr vad som visas i visningsområdet för kartarbetsytan. Kameraalternativ kan skickas in i kartalternativen när de `setCamera` initieras eller skickas in i kartfunktionen.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

I följande kod skapas ett [Map-objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) och alternativen för mitten och zoomning ställs in. Kartegenskaper, till exempel center- och zoomnivå, är en del av [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Skapa en karta via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Skapa en `CameraOptions` karta via </a>av Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ställ in kamera bounds

En begränsningsram kan användas för att uppdatera kartkameran. Om markeringsramen beräknades från punktdata är det ofta användbart att också ange ett pixelutfyllnadsvärde i kameraalternativen för att ta hänsyn till ikonstorleken. Detta kommer att bidra till att säkerställa att punkter inte faller från kanten av kartan viewport.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

I följande kod konstrueras ett `new atlas.Map()` [Map-objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) via . Kartegenskaper som `CameraBoundsOptions` kan definieras via [setCamera-funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) i klassen Map. Egenskaper för bounds och utfyllnad anges med `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Skapa en karta via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Skapa en `CameraBoundsOptions` karta via </a>av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animera kartvyn

När du ställer in kamerans alternativ för kartan kan [animeringsalternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) också ställas in. De här alternativen anger vilken typ av animering och varaktighet det ska ta att flytta kameran.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

I följande kod skapar det första kodblocket en karta och anger mallar för enter- och zoomningskartan. I det andra kodblocket skapas en klickhändelsehanterare för animera-knappen. När du klickar på `setCamera` den här knappen anropas funktionen med några slumpmässiga värden för [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) och [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animera kartvyn' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animera kartvyn</a> av<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Prova koden

Titta på kodexempelna. Du kan redigera JavaScript-koden på **JS-fliken** och se mappningsvyändringarna på **fliken Resultat**. Du kan också klicka **på Redigera på CodePen**, i det övre högra hörnet, och ändra koden i CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions (KameraAlternativ)](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Animeringar](/javascript/api/azure-maps-control/atlas.animationoptions)

Se kodexempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Ändra formatet för kartan](choose-map-style.md)

> [!div class="nextstepaction"]
> [Lägga till kontroller till kartan](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)
