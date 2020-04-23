---
title: Lägg till en HTML-markör till kartan | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till en HTML-markör till en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2700d42c25d58911fb275ad9ce6c5610cd22624d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536776"
---
# <a name="add-html-markers-to-the-map"></a>Lägg till HTML-markörer i kartan

Den här artikeln visar hur du lägger till en anpassad HTML-fil, till exempel en bildfil, till kartan som en HTML-markör.

> [!NOTE]
> HTML-markörer ansluter inte till data källor. I stället läggs information till direkt till markören och markören läggs till i Maps `markers` -egenskapen som är en [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Till skillnad från de flesta skikt i Azure Maps-webbkontroll som använder WebGL för rendering använder HTML-markeringar traditionella DOM-element för rendering. Det gör att fler HTML-markörer som läggs till på en sida, desto fler DOM-element finns. Prestanda kan försämras efter att några hundra HTML-markörer har lagts till. För större data uppsättningar kan du antingen klustra dina data eller använda ett symbol-eller bubbeldiagram.

## <a name="add-an-html-marker"></a>Lägg till en HTML-markör

[HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) -klassen har ett standardformat. Du kan anpassa markören genom att ange färg-och text alternativen för markören. Standardformat för HTML-markören är en SVG-mall som har en `{color}` - `{text}` och-plats hållare. Ange färg och text egenskaper i alternativ för HTML-markören för en snabb anpassning. 

Följande kod skapar en HTML-markör och anger egenskapen Color till "DodgerBlue" och egenskapen text till "10". En popup-meny är kopplad till markören `click` och händelsen används för att växla synligheten för popup-fönstret.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägg till en HTML-markör till en karta' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Lägg till en HTML-markör till en karta</a> genom Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Skapa SVG-mall för HTML-markör

Standardvärdet `htmlContent` för en HTML-markör är en SVG-mall `{color}` med `{text}` plats-mappar och i den. Du kan skapa anpassade SVG-strängar och lägga till samma plats hållare i din SVG så att inställningen `color` och `text` för markören uppdaterar dessa plats hållare i din SVG.

<br/>

<iframe height='500' scrolling='no' title='HTML-markör med anpassad SVG-mall' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>-HTML-markören med anpassad SVG-mall</a> genom<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () i <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps Web SDK innehåller flera SVG-bildmallar som kan användas med HTML-markörer. Mer information finns i dokumentet om [att använda bildmallar](how-to-use-image-templates-web-sdk.md) .

## <a name="add-a-css-styled-html-marker"></a>Lägg till en CSS-formaterad HTML-markör

En av fördelarna med HTML-markeringar är att det finns många fantastiska anpassningar som kan uppnås med CSS. I det här exemplet består innehållet i HtmlMarker av HTML och CSS som skapar en animerad PIN-kod som hamnar på plats och pulsering.

<br/>

<iframe height='500' scrolling='no' title='HTML-datakälla' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>-HTML-datakällan</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Drag bara HTML-markeringar

Det här exemplet visar hur du gör en HTML-markör som kan dras. Stöd `drag`för HTML- `dragstart`märken, `dragend` och-händelser.

<br/>

<iframe height='500' scrolling='no' title='Dragbar HTML-markör' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se den pennan tecknings bara <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>HTML-markören</a> genom<a href='https://codepen.io/azuremaps'>@azuremaps</a>att Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Lägga till mus händelser till HTML-markörer

De här exemplen visar hur du lägger till musen och drar händelser till en HTML-markör.

<br/>

<iframe height='500' scrolling='no' title='Lägga till mus händelser till HTML-markörer' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>att lägga till mus händelser till HTML-markörer genom att</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) i <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Fler kod exempel som kan läggas till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Så här använder du avbildningsmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](./map-add-bubble-layer.md)
