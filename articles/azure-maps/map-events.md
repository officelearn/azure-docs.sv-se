---
title: Hantera händelser med Azure Maps | Microsoft Docs
description: Så här skapar du en interaktiv Web SDK-mappning med kart händelser
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 99ef5aa0ee8fa542b0aa807cc536ebfbee369e10
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484286"
---
# <a name="interact-with-the-map"></a>Interagera med kartan

Den här artikeln visar hur du använder egenskapen [Mappa klass händelser](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) för att markera händelser på kartan och på olika lager i kartan. Det visar också hur du använder egenskapen mappa klass händelser för att markera händelser när du interagerar med en HTML-markör.

## <a name="interact-with-the-map"></a>Interagera med kartan

Spela upp med kartan nedan och se motsvarande mus händelser markerade till höger. Du kan klicka på **fliken JS** för att visa och redigera JavaScript-koden. Du kan också klicka på knappen **Redigera på CodePen** och redigera koden på CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagera med kartan – mus händelser' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interagerar med kartan – mus händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagera med kart skikt

I följande kod visas namnet på de händelser som utlöses när du interagerar med symbol lagret. Symbol-, bubbeldiagram-, linje-och polygon-lagret stöder samma uppsättning händelser. Värme kartan och panel lagren stöder inte någon av dessa händelser.

<br/>

<iframe height='600' scrolling='no' title='Interagera med karta – skikt händelser' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom att <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagera med kartan – skikt händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagera med HTML-markör

Följande kod lägger till Java Script Map-händelser till en HTML-markör. Den markerar även namnet på de händelser som visas när du interagerar med HTML-markören.

<br/>

<iframe height='500' scrolling='no' title='Interagera med händelser för kart-HTML-markör' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>samverkar med kart-HTML-markörens händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I följande tabell visas alla de mappnings klass händelser som stöds.

| Händelse               | Beskrivning |
|---------------------|-------------|
| `boxzoomend`        | Utlöses när interaktionen "box zoom" upphör.|
| `boxzoomstart`      | Utlöses när en "box zoom"-interaktion startar.|
| `click`             | Utlöses när en pekande enhet trycks ned och släpps på samma plats på kartan.|
| `close`             | Utlöses när popup-fönstret stängs manuellt eller program mässigt.|
| `contextmenu`       | Utlöses när användaren klickar på höger musknapp.|
| `data`              | Utlöses när alla kart data läses in eller ändras. |
| `dataadded`         | Utlöses när former läggs till i `DataSource`.|
| `dataremoved`       | Utlöses när former tas bort från `DataSource`.|
| `datasourceupdated` | Utlöses när `DataSource`-objektet uppdateras.|
| `dblclick`          | Utlöses när en pekande enhet klickas två gånger på samma plats på kartan.|
| `drag`              | Utlöses upprepade gånger under en "dra för att panorera"-interaktion på kartan, popup-eller HTML-markören.|
| `dragend`           | Utlöses när en "dra för att panorera"-interaktionen slutar på kartan, popup-eller HTML-markören.|
| `dragstart`         | Utlöses när en "dra för att panorera"-interaktion startar på kartan, popup-eller HTML-markören.|
| `error`             | Utlöses när ett fel inträffar.|
| `idle`              | <p>Utlöses efter att den sista bild rutan renderades innan kartan övergår i tillståndet "inaktive":<ul><li>Ingen kamera över gång pågår.</li><li>Alla begärda paneler har lästs in.</li><li>Alla animeringar av tona/över gångar har slutförts.</li></ul></p>|
| `keydown`           | Utlöses när en tangent trycks ned.|
| `keypress`          | Utlöses när en nyckel som producerar ett typable-tecknet (en ANSI-nyckel) trycks ned.|
| `keyup`             | Utlöses när en nyckel släpps.|
| `layeradded`        | Utlöses när ett lager läggs till i kartan.|
| `layerremoved`      | Utlöses när ett lager tas bort från kartan.|
| `load`              | Utlöses omedelbart efter att alla nödvändiga resurser har hämtats och den första visuella åter givningen av kartan har inträffat.|
| `mousedown`         | Utlöses när ett pekdon trycks på kartan eller när det är ovanpå ett element.|
| `mouseenter`        | Utlöses när en pekande enhet flyttas från början över kartan eller ett element. |
| `mouseleave`        | Utlöses när en pekande enhet flyttas ut från kartan eller ett element. |
| `mousemove`         | Utlöses när en pekande enhet flyttas inom kartan eller ett element.|
| `mouseout`          | Utlöses när en punkt enhet lämnar kart ytans arbets yta lämnar ett element.|
| `mouseover`         | Utlöses när en pekande enhet flyttas över kartan eller ett element.|
| `mouseup`           | Utlöses när ett pekdon släpps i kartan eller när det är ovanpå ett element.|
| `move`              | Utlöses upprepade gånger under en animerad över gång från en vy till en annan, som ett resultat av en användar interaktion eller metoder.|
| `moveend`           | Utlöses strax efter att kartan har slutfört en över gång från en vy till en annan, som ett resultat av användar interaktion eller metoder.|
| `movestart`         | Utlöses precis innan kartan påbörjar en över gång från en vy till en annan, som ett resultat av en användar interaktion eller metoder.|
| `open`              | Utlöses när popup-fönstret öppnas manuellt eller program mässigt.|
| `pitch`             | Utlöses när kartans höjd vinkel (lutning) ändras till följd av användar interaktion eller metoder.|
| `pitchend`          | Aktive ras omedelbart efter det att kartans bredd steg (lutning) har slutförts, som ett resultat av användar interaktion eller metoder.|
| `pitchstart`        | Utlöses när kartans bredd steg (lutning) börjar ändras till följd av användar interaktion eller metoder.|
| `ready`             | Utlöses när de lägsta obligatoriska mappnings resurserna läses in innan kartan är klar att program mässigt interagera med.|
| `render`            | <p>Utlöses när kartan ritas till skärmen, till följd av:<ul><li>En ändring av kartans position, zoomning, färgdjup eller förhöjd.</li><li>En ändring av kartans format.</li><li>En ändring i en `DataSource` källa.</li><li>Inläsningen av en vektor panel, en multijson-fil, ett specialtecken eller en sprite.</li></ul></p>|
| `resize`            | Utlöses omedelbart efter att kart storleken har ändrats.|
| `rotate`            | Utlöses upprepade gånger under en "dra för att rotera"-interaktion.|
| `rotateend`         | Utlöses när interaktionen "dra för att rotera" upphör.|
| `rotatestart`       | Utlöses när interaktionen "dra och rotera" startar.|
| `shapechanged`      | Utlöses när en form objekt egenskap ändras.|
| `sourcedata`        | Utlöses när en av kartans källor läses in eller ändras, inklusive om en panel som tillhör en källa läses in eller ändras. |
| `sourceadded`       | Utlöses när en `DataSource` eller `VectorTileSource` läggs till i kartan.|
| `sourceremoved`     | Utlöses när en `DataSource` eller `VectorTileSource` tas bort från kartan.|
| `styledata`         | Utlöses när kartans format läses in eller ändras.|
| `styleimagemissing` | Utlöses när ett lager försöker läsa in en bild från den bild-Sprite som inte finns |
| `tokenacquired`     | Utlöses när en AAD-åtkomsttoken hämtas.|
| `touchcancel`       | Utlöses när en touchcancel-händelse inträffar inom kartan.|
| `touchend`          | Utlöses när en touchEnd-händelse inträffar inom kartan.|
| `touchmove`         | Utlöses när en touchMove-händelse inträffar inom kartan.|
| `touchstart`        | Utlöses när en touchstart-händelse inträffar inom kartan.|
| `wheel`             | Utlöses när en mus hjuls händelse inträffar i kartan.|
| `zoom`              | Utlöses upprepade gånger under en animerad över gång från en zoomnings nivå till en annan, som ett resultat av användar interaktion eller metoder.|
| `zoomend`           | Utlöses strax efter att kartan har slutfört en över gång från en zoomnivå till en annan, som ett resultat av användar interaktion eller metoder.|
| `zoomstart`         | Utlöses precis innan kartan påbörjar en över gång från en zoomnings nivå till en annan, som ett resultat av användar interaktion eller metoder.|


## <a name="next-steps"></a>Nästa steg

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Använda modulen Azure Maps tjänster](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)
