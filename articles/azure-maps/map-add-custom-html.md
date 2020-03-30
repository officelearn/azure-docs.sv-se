---
title: Lägg till en HTML-markör för att mappa | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur du lägger till en HTML-markör på en karta med Hjälp av Microsoft Azure Maps Web SDK.
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
# <a name="add-html-markers-to-the-map"></a>Lägga till HTML-markörer på kartan

I den här artikeln visas hur du lägger till en anpassad HTML-kod, till exempel en bildfil på kartan som en HTML-markör.

> [!NOTE]
> HTML-markörer ansluter inte till datakällor. I stället läggs positionsinformation direkt till markören och `markers` markören läggs till i egenskapen kartor som är en [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Till skillnad från de flesta lager i Azure Maps Web control som använder WebGL för rendering använder HTML-markörer traditionella DOM-element för rendering. Ju fler HTML-markörer som läggs till på en sida, desto fler DOM-element finns det. Prestanda kan försämras efter att ha lagt till några hundra HTML-markörer. För större datauppsättningar bör du överväga att antingen gruppera dina data eller använda ett symbol- eller bubbellager.

## <a name="add-an-html-marker"></a>Lägga till en HTML-markör

Klassen [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) har ett standardformat. Du kan anpassa markören genom att ange färg- och textalternativen för markören. Standardformatet för klassen HTML-markör är en `{color}` `{text}` SVG-mall som har en och platshållare. Ange färg- och textegenskaperna i HTML-marköralternativen för en snabb anpassning. 

Följande kod skapar en HTML-markör och anger färgegenskapen till "DodgerBlue" och textegenskapen till "10". En popup är kopplad till `click` markören och händelsen används för att växla synligheten för popup.A popup isached to the marker and event is used to växla synligheten för popup.A popup isached to the marker and event is used to växla synligheten för popup.a popup isached to the marker and event is used

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

Nedan visas det fullständiga kodexemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en HTML-markör på en karta' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Lägg till en HTML-markör på en karta</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Skapa SVG-mallad HTML-markör

Standardvärdet `htmlContent` för en Html-markör är en `{color}` `{text}` SVG-mall med platsmappar och i den. Du kan skapa anpassade SVG-strängar och lägga till samma platshållare `color` `text` i svg så att du anger och alternativen för markören uppdaterar dessa platshållare i svg-enheten.

<br/>

<iframe height='500' scrolling='no' title='HTML-markör med anpassad SVG-mall' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>html-markör för penna med anpassad SVG-mall</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps web SDK innehåller flera SVG-avbildningsmallar som kan användas med HTML-markörer. Mer information finns i dokumentet [Så här använder du bildmallar.](how-to-use-image-templates-web-sdk.md)

## <a name="add-a-css-styled-html-marker"></a>Lägga till en CSS-formaterad HTML-markör

En av fördelarna med HTML-markörer är att det finns många bra anpassningar som kan uppnås med css. I det här exemplet består innehållet i HtmlMarker av HTML och CSS som skapar en animerad stift som faller på plats och pulserar.

<br/>

<iframe height='500' scrolling='no' title='HTML-datakälla' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Läpbara HTML-markörer som kan dras

Det här exemplet visar hur du gör en HTML-markör dragen. HTML-markörer `drag` `dragstart`stöder `dragend` , och händelser.

<br/>

<iframe height='500' scrolling='no' title='Läpbar HTML-markör' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se den <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>penrerbara HTML-markören</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Lägga till mushändelser i HTML-markörer

De här exemplen visar hur du lägger till mus- och drarhändelser till en HTML-markör.

<br/>

<iframe height='500' scrolling='no' title='Lägga till mushändelser i HTML-markörer' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Lägga till mushändelser i HTML-markörer</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [HtmlMarker (på samma sätt som)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Mer från HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Fler kodexempel att lägga till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Så här använder du avbildningsmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](./map-add-bubble-layer.md)
