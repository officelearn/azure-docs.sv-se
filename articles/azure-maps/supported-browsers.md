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
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738396"
---
# <a name="web-sdk-supported-browsers"></a>Webbläsare som stöds av webb-SDK

Azure Maps Web SDK tillhandahåller en hjälpfunktionen kallas [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Den här funktionen identifierar om en webbläsare har den minsta uppsättningen WebGL funktioner som krävs för att stödja inläsning och rendering kartkontrollen. Här är ett exempel på hur du använder funktionen:

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Skrivbord

Azure Maps Web SDK har stöd för följande webbläsare:

- Microsoft Edge (aktuella och föregående versionen)
- Google Chrome (aktuella och föregående versionen)
- Mozilla Firefox (aktuella och föregående versionen)
- Apple Safari (Mac OS X) (aktuella och tidigare version)

Se även [rikta äldre webbläsare](#Target-Legacy-Browsers) senare i den här artikeln.

## <a name="mobile"></a>Mobil

Azure Maps Web SDK har stöd för följande mobila webbläsare:

- Android
  - Aktuell version av Chrome på Android 6.0 och senare
  - Chrome WebView på Android 6.0 och senare
- iOS
  - Mobile Safari på den aktuella och tidigare huvudversionen av iOS
  - UIWebView och WKWebView på den aktuella och tidigare huvudversionen av iOS
  - Aktuell version av Chrome för iOS

> [!TIP]
> Om du bäddar in en karta i ett mobilt program med hjälp av en WebView-kontroll, kanske du föredrar att använda den [npm-paket för Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) i stället för att referera till versionen av SDK: N som finns på Azure Content Delivery Nätverk. Den här metoden minskar inläsningstid eftersom SDK: N är redan finnas på användarens enhet och behöver inte hämtas vid körning.

## <a name="nodejs"></a>Node.js

Följande Web SDK moduler stöds även i Node.js:

- Services-modul ([dokumentation](how-to-use-services-module.md) | [npm-modulen](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Target äldre webbläsare

Du kanske vill rikta äldre webbläsare som inte stöder WebGL eller som endast har begränsat stöd för den. I sådana fall rekommenderar vi att du använder Azure Maps-tjänsterna tillsammans med en kartkontroll med öppen källkod som [informationsblad](https://leafletjs.com/). Här är ett exempel:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + informationsblad" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + informationsblad</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Kartkontroll](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Services-modul](how-to-use-services-module.md)
