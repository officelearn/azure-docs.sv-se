---
title: Lägga till en HTML-markör i Azure Maps | Microsoft Docs
description: Lägga till en HTML-markör i Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1c812a77429e13ea39b2f4946043c13e10aaf097
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993750"
---
# <a name="add-html-markers-to-the-map"></a>Lägga till HTML-markeringar på kartan

Den här artikeln visar hur du lägger till en anpassad HTML till exempel en bildfil på kartan som en HTML-markör.

> [!NOTE]
> HTML-markeringar ansluter inte till datakällor. I stället position information läggs direkt till markörens och markören har lagts till i maps `markers` egenskap som är en [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Till skillnad från de flesta lager i Azure Maps webbkontrollen som använder WebGL för rendering använder HTML-markeringar traditionella DOM-element för rendering. Det innebär mer HTML-markörer lagt till en sida, mer DOM-element som finns. Prestanda kan försämras när du lagt till några hundra HTML-markörer. Överväg att antingen klustring dina data eller med en Symbol- eller bubbeldiagram lager för större datauppsättningar.

## <a name="add-an-html-marker"></a>Lägg till en HTML-markör

HtmlMarker klassen har ett standardformat. Du kan anpassa markörens genom att ange alternativ för färg och text för markören. Standardformatet för klassen HtmlMarker är en SVG-mall som har en platshållare för färg och text. Ange egenskaper för färg och text i HtmlMarker alternativ för en snabb anpassning. 

<br/>

<iframe height='500' scrolling='no' title='Lägg till en HTML-markör till en karta' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Lägg till en HTML-markör till en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra blockeringen av kod lägger till en [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) i kartan med hjälp av den [markörer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) egenskapen för den [kartan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) klass. HtmlMarker läggs till i kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att den visas när kartan har lästs in helt.

## <a name="create-svg-templated-html-marker"></a>Skapa SVG mallbaserade HTML-markör

Standard `htmlContent` en HTML-markör är en SVG-mall med plats mappar `{color}` och `{text}` i den. Du kan skapa anpassade SVG-strängar och lägga till dessa samma platshållare i din SVG så att ställa in den `color` och `text` alternativ för markören uppdatera dessa platshållare i din SVG.

<br/>

<iframe height='500' scrolling='no' title='HTML-markör med anpassade SVG-mall' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML markör med anpassade SVG mall</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Lägg till en CSS-formaterat HTML markör

En av fördelarna med HTML markörer är att det finns många bra anpassningar som kan uppnås med hjälp av CSS. I det här exemplet består innehållet i HtmlMarker med HTML och CSS som skapar en animerade PIN-kod som släpper och pulser.

<br/>

<iframe height='500' scrolling='no' title='HTML-datakällan' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Den fyrkantiga HTML-markeringar

Det här exemplet visar hur du gör en HTML-markör fyrkantiga. Stöd för HTML-markeringar `drag`, `dragstart` och `dragend` händelser.

<br/>

<iframe height='500' scrolling='no' title='Den fyrkantiga HTML markör' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>fyrkantiga HTML-markör</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Lägga till mushändelser i HTML-markeringar

De här exemplen visar hur du lägger till musen och dra händelser till en HTML-markör.

<br/>

<iframe height='500' scrolling='no' title='Att lägga till mushändelser i HTML-markeringar' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>att lägga till mushändelser i HTML-markeringar</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar:

> [!div class="nextstepaction"]
> [Lägg till en symbol-lager](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till ett bubbeldiagram lager](./map-add-bubble-layer.md)