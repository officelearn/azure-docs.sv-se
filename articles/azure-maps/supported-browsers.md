---
title: Webb-SDK stöds webbläsare – Azure Maps | Microsoft Docs
description: Läs mer om vilka webbläsare som stöds för Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: bc876fbf0eb15f887d57d4ddcca2301ef7233afa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577347"
---
# <a name="web-sdk-supported-browsers"></a>Webbläsare som stöds av webb-SDK

Azure Maps Web SDK tillhandahåller en hjälpfunktionen [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-) krävs för att identifiera om en webbläsare har minst WebGL funktioner som stöder inläsning och rendering kartkontrollen. 

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    //Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Skrivbord

Azure Maps Web SDK har stöd för följande webbläsare.

- Den aktuella och tidigare versionen av Microsoft Edge 
- Den aktuella och tidigare versionen av Chrome 
- Den aktuella och tidigare versionen av Firefox 
- Den aktuella och tidigare versionen av Safari (Mac OS X) 

Se även [rikta äldre webbläsare](#Target-Legacy-Browsers).

## <a name="mobile"></a>Mobil

Azure Maps Web SDK har stöd för följande mobila webbläsare.

-  Android
    * Aktuell version av Chrome på Android 6.0 +
    * Chrome WebView på Android 6.0 +
- iOS
    * Mobile Safari på den aktuella och tidigare huvudversionen av iOS
    * UIWebView och WKWebView på den aktuella och tidigare huvudversionen av iOS
    * Aktuell version av Chrome för iOS

> [!TIP]
> Om du bäddar in en karta i ett mobilt program som använder en WebView-kontroll, kanske du föredrar att använda den [npm-paket för Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) i stället för att referera till CDN som värd-versionen av SDK. Detta minskar inläsningstid eftersom SDK: N ska redan finnas på användarens enhet och inte behöver hämtas vid körning.

## <a name="nodejs"></a>Node.js

Följande Web SDK moduler stöds även i Node.js:

- Services-modul ([dokumentation](how-to-use-services-module.md) | [npm-modulen](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Target äldre webbläsare

Om du vill rikta äldre webbläsare som stöder inte eller har begränsat stöd för WebGL rekommenderar vi att du använder Azure Maps-tjänsterna i kombination med en kartkontroll med öppen källkod som [informationsblad](https://leafletjs.com/). 


<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + informationsblad" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + informationsblad</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Kartkontroll](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Services-modul](how-to-use-services-module.md)
