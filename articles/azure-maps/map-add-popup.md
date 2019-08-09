---
title: Lägg till en popup med Azure Maps | Microsoft Docs
description: Så här lägger du till en popup-mapp i Java Script Map
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: caf661faf00d1d32664b7958a14a8719a37ab36e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882099"
---
# <a name="add-a-popup-to-the-map"></a>Lägg till en popup till kartan

Den här artikeln visar hur du lägger till en popup-meny till en plats på en karta.

## <a name="understand-the-code"></a>Förstå koden

<a id="addAPopup"></a>

Följande kod lägger till en punkt funktion, som har `name` och `description` egenskaper, till kartan med ett symbol lager. En instans av [klassen pop](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) skapas men visas inte. Mus händelser läggs till i symbol lagret för att utlösa öppning och stängning när musen hovrar över och ut från symbol markören. När `position` markör symbolen har hovrat uppdateras popup `name` -egenskapen med positionen för markören `content` och alternativet uppdateras med en del HTML som radbryts och `description` egenskaperna för punkt funktionen har hovras. Popup-fönstret visas sedan på kartan med dess `open` funktion.

<br/>

<iframe height='500' scrolling='no' title='Lägg till ett popup med Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Lägg till ett popup-fönster med Azure Maps</a> av Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Återanvända en popup med flera punkter

När du har ett stort antal punkter och bara vill visa en popup i taget, är det bästa sättet att skapa ett popup-fönster och återanvända det i stället för att skapa en popup för varje punkt funktion. Genom att återanvända popup-fönstret minskar antalet DOM-element som skapats av programmet avsevärt vilket kan ge bättre prestanda. I följande exempel skapas funktioner i tre punkter. Om du klickar på någon av dem visas en popup med innehållet för den punkt funktionen.

<br/>

<iframe height='500' scrolling='no' title='Återanvända popup med flera PIN-bara' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se popup-fönstret för att <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>använda pennan med flera stift</a> genom<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Anpassa en popup

Som standard har popup-fönstret en vit bakgrund, en pekare längst ned och en stängnings knapp i det övre högra hörnet. Följande exempel ändrar bakgrunds färgen till svart med `fillColor` alternativet i popup-fönstret. Knappen Stäng tas bort genom att `shoCloseButton` ställa in alternativet på falskt. HTML-innehållet i popup-fönstret använder utfyllda 10 pixlar från kanten på popup-fönstret och texten blir vit så att den visas snyggt på den svarta bakgrunden.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Anpassad popup" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se popup-fönstret för <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>anpassad</a> penna efter<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Popup-händelser

Popup-fönster kan öppnas, stängas och dras. Popup-klassen innehåller händelser för hjälp utvecklare som reagerar på dessa åtgärder. I följande exempel markeras vilka händelser som ska utlösas när du öppnar, stänger eller drar i popup-fönstret. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup-händelser" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se popup- <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>händelser</a> för penna genom att<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Motta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Se följande fantastiska artiklar för fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Lägg till ett symbol lager](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till en HTML-markör](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Lägg till en form](./map-add-shape.md)