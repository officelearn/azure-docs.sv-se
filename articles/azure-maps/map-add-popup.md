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
ms.openlocfilehash: 92d44ef3d0db8e93d4babd7441238c7fa105dbd5
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639003"
---
# <a name="add-a-popup-to-the-map"></a>Lägg till en popup till kartan

Den här artikeln visar hur du lägger till en popup-meny till en plats på en karta.

## <a name="understand-the-code"></a>Förstå koden

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Lägg till ett popup med Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Lägg till ett popup-fönster med Azure Maps</a> av Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar det första blocket kod ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner. Det skapar också HTML-innehåll som ska visas i popup-fönstret.

Det andra blocket kod skapar ett data käll objekt med hjälp av [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -klassen. En punkt är en [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) i klassen [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) . Ett Point-objekt med namn och beskrivnings egenskaper skapas sedan och läggs till i data källan.

Ett [symbol lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att återge punktbaserade data i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan.  Ett symbol lager skapas i det tredje blocket med kod. Data källan läggs till i symbol skiktet, som sedan läggs till i kartan.

Den fjärde kod blocket skapar ett [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) -objekt `new atlas.Popup()`via. Egenskaper för popup-fönster som position och pixelOffset är en del av [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions). PopupOptions kan definieras i popup-konstruktorn eller via funktionen [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) i popup-klassen. En `mouseover` händelse lyssnare för symbol lagret skapas.

Det sista blocket kod skapar en funktion som utlöses av `mouseover` händelse lyssnaren. Den anger popup-innehåll och egenskaper för popup-fönstret och lägger till ett popup-objekt till kartan.

## <a name="reusing-a-popup-with-multiple-points"></a>Återanvända en popup med flera punkter

När du har många punkter och bara vill visa en popup i taget, är det bästa sättet att skapa ett popup-fönster och återanvända det i stället för att skapa en popup för varje punkt funktion. Genom att göra detta minskar antalet DOM-element som skapats av programmet avsevärt vilket kan ge bättre prestanda. Det här exemplet skapar tre punkt funktioner. Om du klickar på någon av dem visas en popup med innehållet för den punkt funktionen.

<br/>

<iframe height='500' scrolling='no' title='Återanvända popup med flera PIN-bara' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se popup-fönstret för att <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>använda pennan med flera stift</a> genom<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
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