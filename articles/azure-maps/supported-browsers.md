---
title: Webbläsare som stöds av Web SDK | Microsoft Azure Maps
description: I den här artikeln får du lära dig om webbläsare som stöds för Microsoft Azure Maps-webbsdk och hur du kontrollerar om en webbläsare är en webbläsare som stöds.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6a67a358d45ac61c955c26f495169f7cd8364074
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910450"
---
# <a name="web-sdk-supported-browsers"></a>Webbläsare som stöds av webb-SDK

Azure Maps Web SDK innehåller en hjälp funktion som kallas [Atlas. isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Den här funktionen identifierar om en webbläsare har en minsta uppsättning WebGL-funktioner som krävs för att stödja inläsning och åter givning av kart kontrollen. Här är ett exempel på hur du använder funktionen:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Skrivbord

Azure Maps Web SDK stöder följande Skriv bords webbläsare:

- Microsoft Edge (aktuell och föregående version)
- Google Chrome (aktuell och föregående version)
- Mozilla Firefox (aktuell och föregående version)
- Apple Safari (Mac OS X) (aktuell och tidigare version)

Se även [mål äldre webbläsare](#Target-Legacy-Browsers) längre fram i den här artikeln.

## <a name="mobile"></a>Mobilt

Azure Maps Web SDK stöder följande mobila webbläsare:

- Android
  - Aktuell version av Chrome på Android 6,0 och senare
  - Chrome webbvy på Android 6,0 och senare
- iOS
  - Mobile Safari på den aktuella och föregående huvud versionen av iOS
  - UIWebView och WKWebView på den aktuella och den tidigare huvud versionen av iOS
  - Aktuell version av Chrome för iOS

> [!TIP]
> Om du bäddar in en karta inuti ett mobilt program med hjälp av en WebView-kontroll, kanske du föredrar att använda [NPM-paketet i Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) i stället för att referera till den version av SDK som finns på Azure Content Delivery Network. Den här metoden minskar inläsnings tiden eftersom SDK redan finns på användarens enhet och inte behöver laddas ned vid körning.

## <a name="nodejs"></a>Node.js

Följande Web SDK-moduler stöds också i Node. js:

- Modulen tjänster ([dokumentation](how-to-use-services-module.md) | [NPM module](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Fokusera på äldre webbläsare

Du kanske vill rikta in dig på äldre webbläsare som inte stöder WebGL eller som bara har begränsat stöd för den. I sådana fall rekommenderar vi att du använder Azure Maps Services tillsammans med en kart kontroll med öppen källkod som [broschyr](https://leafletjs.com/). Här är ett exempel:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + broschyr" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + broschyr</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Kartkontroll](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Tjänstmodul](how-to-use-services-module.md)
