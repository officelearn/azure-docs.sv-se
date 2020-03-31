---
title: Webbläsare för Web SDK-stödda | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om webbläsare som stöds för Microsoft Azure Maps Web SDK och hur du kontrollerar om en webbläsare är en webbläsare som stöds.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988795"
---
# <a name="web-sdk-supported-browsers"></a>Webbläsare som stöds av webb-SDK

Azure Maps Web SDK tillhandahåller en hjälpfunktion som kallas [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-). Den här funktionen identifierar om en webbläsare har den minsta uppsättning WebGL-funktioner som krävs för att stödja inläsning och rendering av kartkontrollen. Här är ett exempel på hur du använder funktionen:

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

Azure Maps Web SDK stöder följande skrivbordswebbläsare:

- Microsoft Edge (aktuell och tidigare version)
- Google Chrome (aktuell och tidigare version)
- Mozilla Firefox (nuvarande och tidigare version)
- Apple Safari (Mac OS X) (aktuell och tidigare version)

Se även [Mållevande webbläsare](#Target-Legacy-Browsers) senare i den här artikeln.

## <a name="mobile"></a>Mobil

Azure Maps Web SDK stöder följande mobila webbläsare:

- Android
  - Aktuell version av Chrome på Android 6.0 och senare
  - Chrome WebView på Android 6.0 och senare
- iOS
  - Mobile Safari på nuvarande och tidigare huvudversion av iOS
  - UIWebView och WKWebView på den aktuella och tidigare huvudversionen av iOS
  - Aktuell version av Chrome för iOS

> [!TIP]
> Om du bäddar in en karta i ett mobilprogram med hjälp av en WebView-kontroll kanske du föredrar att använda [npm-paketet för Azure Maps Web SDK](https://www.npmjs.com/package/azure-maps-control) i stället för att referera till den version av SDK som finns på Azure Content Delivery Network. Den här metoden minskar inläsningstiden eftersom SDK redan finns på användarens enhet och inte behöver hämtas vid körning.

## <a name="nodejs"></a>Node.js

Följande Web SDK-moduler stöds också i Node.js:

- Tjänstemodul ([dokumentation](how-to-use-services-module.md) | [npm modul](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Rikta in äldre webbläsare

Du kanske vill rikta in dig på äldre webbläsare som inte stöder WebGL eller som bara har begränsat stöd för det. I sådana fall rekommenderar vi att du använder Azure Maps-tjänster tillsammans med en kartkontroll med öppen källkod som [Broschyr](https://leafletjs.com/). Här är ett exempel:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Kartor + Broschyr" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps +</a> Leaflet<a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Kartkontroll](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Tjänstmodul](how-to-use-services-module.md)
