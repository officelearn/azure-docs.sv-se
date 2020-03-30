---
title: Visa trafik på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du visar trafikdata på en karta med hjälp av Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534822"
---
# <a name="show-traffic-on-the-map"></a>Visa trafik på kartan

Det finns två typer av trafikdata som är tillgängliga i Azure Maps:

- Incidentdata - består av punkt- och linjebaserade data för bland annat konstruktion, vägavstängningar och olyckor.
- Flödesdata - ger mått på trafikflödet på vägarna. Ofta används trafikflödesdata för att färga vägarna. Färgerna baseras på hur mycket trafik som saktar ned flödet, i förhållande till hastighetsgränsen eller ett annat mått. Trafikflödesdata i Azure Maps har tre olika mätvärden:
    - `relative`- är i förhållande till vägens hastighet fritt flöde.
    - `absolute`- är den absoluta hastigheten för alla fordon på vägen.
    - `relative-delay`- visar områden som är långsammare än den genomsnittliga förväntade förseningen.

Följande kod visar hur du visar trafikdata på kartan.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Nedan visas det fullständiga kodexemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Visa trafik på en karta' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Visa trafik på en</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>karta av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Alternativ för överlagring av trafik

Med följande verktyg kan du växla mellan de olika trafiköverlagringsinställningarna för att se hur renderingen ändras. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Alternativ för överlagring av trafik" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se alternativen för <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>överlagring</a> av<a href='https://codepen.io/azuremaps'>@azuremaps</a>pentrafik av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafikOptioner](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Förbättra dina användarupplevelser:

> [!div class="nextstepaction"]
> [Kartinteraktion med mushändelser](map-events.md)

> [!div class="nextstepaction"]
> [Bygga en tillgänglig karta](map-accessibility.md)

> [!div class="nextstepaction"]
> [Exempelsida för kod](https://aka.ms/AzureMapsSamples)
