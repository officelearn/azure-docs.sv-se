---
title: Hantera karthändelser | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du gör en interaktiv Web SDK-karta med karthändelser med hjälp av Microsoft Azure Maps web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534906"
---
# <a name="interact-with-the-map"></a>Interagera med kartan

Den här artikeln visar hur du använder [klassen Map Events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events). Egenskapen markerar händelser på kartan och på olika lager av kartan. Du kan också markera händelser när du interagerar med en HTML-markör.

## <a name="interact-with-the-map"></a>Interagera med kartan

Spela med kartan nedan och se motsvarande mushändelser markerade till höger. Du kan klicka på **fliken JS** för att visa och redigera JavaScript-koden. Du kan också klicka på **Redigera på CodePen** för att ändra koden på CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagera med kartan – mushändelser' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pennan <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interagera med kartan – mushändelser</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagera med kartlager

Följande kod markerar den avfyrade händelsen när du interagerar med symbollagret. Symbol-, bubbel-, linje- och polygonskiktet stöder alla samma uppsättning händelser. Värmekartan och kakellagren stöder inte någon av dessa händelser.

<br/>

<iframe height='600' scrolling='no' title='Interagera med kartan – Lagerhändelser' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagera med kartan – Lagerhändelser</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagera med HTML-markör

Följande kod lägger till Javascript-karthändelser i en HTML-markör. Det belyser också namnet på de händelser som får eldas upp när du interagerar med HTML-markör.

<br/>

<iframe height='500' scrolling='no' title='Interagera med kartan - HTML-markörhändelser' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interagera med kartan - HTML-markörhändelser</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I följande tabell visas alla kartklasshändelser som stöds.

| Händelse               | Beskrivning |
|---------------------|-------------|
| `boxzoomend`        | Utlöses när en "box zoom" interaktion slutar.|
| `boxzoomstart`      | Utlöses när en "box zoom" interaktion startar.|
| `click`             | Utlöses när en pekdon trycks in och släpps på samma punkt på kartan.|
| `close`             | Utlöses när popup stängs manuellt eller programatiskt.|
| `contextmenu`       | Sparken när den högra knappen på musen klickas.|
| `data`              | Utlöses när kartdata läses in eller ändras. |
| `dataadded`         | Utlöses när former `DataSource`läggs till i .|
| `dataremoved`       | Utlöses när former `DataSource`tas bort från .|
| `datasourceupdated` | Utlöses `DataSource` när objektet uppdateras.|
| `dblclick`          | Utlöses när en pekdon klickas två gånger på samma punkt på kartan.|
| `drag`              | Utlöses upprepade gånger under en "dra till panorering" interaktion på kartan, popup eller HTML-markör.|
| `dragend`           | Utlöses när en "dra till panorering" interaktion slutar på kartan, popup eller HTML-markör.|
| `dragstart`         | Utlöses när en "dra till panorering" interaktion börjar på kartan, popup eller HTML-markör.|
| `error`             | Utlöses när ett fel inträffar.|
| `idle`              | <p>Utlöses efter den sista bildrutan som återges innan kartan går in i ett "inaktivt" tillstånd:<ul><li>Inga kameraövergångar pågår.</li><li>Alla för närvarande begärda paneler har lästs in.</li><li>Alla fade/transition-animeringar har slutförts.</li></ul></p>|
| `keydown`           | Avfyras när en knapp trycks ned.|
| `keypress`          | Utlöses när en tangent som ger ett stavbart tecken (en ANSI-tangent) trycks ned.|
| `keyup`             | Sparken när en nyckel släpps.|
| `layeradded`        | Utlöses när ett lager läggs till på kartan.|
| `layerremoved`      | Utlöses när ett lager tas bort från kartan.|
| `load`              | Sparken omedelbart efter att alla nödvändiga resurser har hämtats och den första visuellt fullständiga återgivningen av kartan har inträffat.|
| `mousedown`         | Utlöses när en pekdon trycks in på kartan eller ovanpå ett element.|
| `mouseenter`        | Utlöses när en pekdon först flyttas över kartan eller ett element. |
| `mouseleave`        | Utlöses när en pekdon flyttas ut från kartan eller ett element. |
| `mousemove`         | Utlöses när en pekdon flyttas inom kartan eller ett element.|
| `mouseout`          | Avfyras när en punkt enhet lämnar kartans duk våra lämnar ett element.|
| `mouseover`         | Utlöses när en pekdon flyttas över kartan eller ett element.|
| `mouseup`           | Utlöses när en pekdon släpps på kartan eller ovanpå ett element.|
| `move`              | Utlöses upprepade gånger under en animerad övergång från en vy till en annan, som ett resultat av antingen användarinteraktion eller metoder.|
| `moveend`           | Utlöses strax efter att kartan har slutfört en övergång från en vy till en annan, som ett resultat av antingen användarinteraktion eller metoder.|
| `movestart`         | Utlöses strax innan kartan börjar en övergång från en vy till en annan, som ett resultat av antingen användarinteraktion eller metoder.|
| `open`              | Utlöses när popup öppnas manuellt eller programatiskt.|
| `pitch`             | Utlöses när kartans tonhöjd (lutning) ändras som ett resultat av antingen användarinteraktion eller metoder.|
| `pitchend`          | Utlöses omedelbart efter att kartans tonhöjd (lutning) har ändrats till följd av antingen användarinteraktion eller metoder.|
| `pitchstart`        | Utlöses när kartans tonhöjd (lutning) börjar en förändring som ett resultat av antingen användarinteraktion eller metoder.|
| `ready`             | Utlöses när de minsta nödvändiga kartresurserna läses in innan kartan är redo att interageras programmässigt med.|
| `render`            | <p>Sparken när kartan ritas till skärmen, som ett resultat av:<ul><li>En ändring av kartans position, zoom, tonhöjd eller lager.</li><li>En ändring av kartans stil.</li><li>En ändring `DataSource` av en källa.</li><li>Inläsningen av en vektorpanel, GeoJSON-fil, glyf eller sprite.</li></ul></p>|
| `resize`            | Avfyrad direkt efter att kartan har ändrats.|
| `rotate`            | Utlöses upprepade gånger under en "dra för att rotera" interaktion.|
| `rotateend`         | Utlöses när en "dra för att rotera" interaktion slutar.|
| `rotatestart`       | Utlöses när en "dra för att rotera" interaktion startar.|
| `shapechanged`      | Utlöses när en formobjektegenskap ändras.|
| `sourcedata`        | Utlöses när en av kartans källor läses in eller ändras, inklusive om en bricka som tillhör en källa läses in eller ändras. |
| `sourceadded`       | Utlöses `DataSource` när `VectorTileSource` en eller läggs till på kartan.|
| `sourceremoved`     | Utlöses `DataSource` när `VectorTileSource` en eller tas bort från kartan.|
| `styledata`         | Utlöses när kartans stil laddas eller ändras.|
| `styleimagemissing` | Utlöses när ett lager försöker läsa in en bild från bilden sprite som inte finns |
| `tokenacquired`     | Utlöses när en AAD-åtkomsttoken hämtas.|
| `touchcancel`       | Utlöses när en touchcancel-händelse inträffar på kartan.|
| `touchend`          | Utlöses när en touchend-händelse inträffar på kartan.|
| `touchmove`         | Utlöses när en touchmove-händelse inträffar på kartan.|
| `touchstart`        | Utlöses när en touchstart-händelse inträffar på kartan.|
| `wheel`             | Utlöses när en mushjulshändelse inträffar på kartan.|
| `zoom`              | Utlöses upprepade gånger under en animerad övergång från en zoomnivå till en annan, som ett resultat av antingen användarinteraktion eller metoder.|
| `zoomend`           | Utlöses strax efter att kartan har slutfört en övergång från en zoomnivå till en annan, som ett resultat av antingen användarinteraktion eller metoder.|
| `zoomstart`         | Utlöses strax innan kartan börjar en övergång från en zoomnivå till en annan, som ett resultat av antingen användarinteraktion eller metoder.|


## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fullständiga kodexempel:

> [!div class="nextstepaction"]
> [Använda Modulen Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)
