---
title: Visa trafik med Azure Maps | Microsoft Docs
description: Visa trafik data på Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b765f6f35cecfdd3183b4432954fcfb2548de046
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874866"
---
# <a name="show-traffic-on-the-map"></a>Visa trafik på kartan

Det finns två typer av trafik data tillgängliga i Azure Maps:

- Incident data – består av punkt-och linjebaserade data för sådant som konstruktion, väg stängningar och olyckor.
- Flow-data – innehåller mått för trafik flödet på vägarna. Trafik flödes data används ofta för att färga vägarna baserat på hur mycket trafik som saktar ned flödet i förhållande till hastighets gränsen eller något annat mått. Trafik flödes data i Azure Maps har tre olika mått mått:
    - `relative`– är i förhållande till vägens fria flödes hastighet.
    - `absolute`– är den absoluta hastigheten hos alla fordon på vägen.
    - `relative-delay`– visar områden som är långsammare än den genomsnittliga fördröjningen.

Följande kod visar hur du visar trafik data på kartan.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Visa trafik på en karta' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Visa trafik på en karta</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Alternativ för trafik överlägg

Med följande verktyg kan du växla mellan olika inställningar för trafik överlägg för att se hur åter givningen förändras. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Alternativ för trafik överlägg" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se alternativen för rit Överlagrings <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>trafik</a> genom<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Förbättra användar upplevelsen:

> [!div class="nextstepaction"]
> [Mappa interaktion med mus händelser](map-events.md)

> [!div class="nextstepaction"]
> [Skapa en tillgänglig karta](map-accessibility.md)

> [!div class="nextstepaction"]
> [Kod exempel sida](https://aka.ms/AzureMapsSamples)
