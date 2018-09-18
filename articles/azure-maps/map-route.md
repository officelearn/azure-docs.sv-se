---
title: Visa riktningar med Azure Maps | Microsoft Docs
description: Så här visar du riktningarna mellan två platser på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729071"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B

Den här artikeln visar hur du gör en begäran om väg och visa rutten på kartan.

Det finns två sätt att göra detta. Det första sättet är att fråga den [API: T för Azure Maps-väg](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) via en tjänst-modul. Det andra sättet är att göra en [XMLHttpRequest](https://xhr.spec.whatwg.org/) -API: et. Båda metoderna beskrivs nedan.

## <a name="query-the-route-via-service-module"></a>Fråga route via tjänstemodulen

<iframe height='500' scrolling='no' title='Visa riktningar från A till B på en karta (Tjänstemodulen)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/RBZbep/'>visar riktningar från A till B på en karta (Tjänstemodulen)</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Raden i det andra kodblocket skapar en instans av en tjänsteklient.

Det tredje kodblocket initierar den [rad sträng Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) på kartan.

Det fjärde kodblocket skapar och lägger till PIN-koder på kartan för att representera start- och slutpunkt för rutten. Du kan se [lägga till en PIN-kod på kartan](map-add-pin.md) anvisningar om hur du använder [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Nästa kodblock i använder [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funktion i klassen kartan för att ställa in avgränsningsruta för kartan baserat på start- och slutpunkt för rutten.

Det sjätte kodblocket skapas en väg.

Senaste kodblocket frågar Azure Maps-tjänst för händelsedirigering via den [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metod för att hämta en väg mellan start- och mål. Svaret parsas sedan till GeoJSON-format med den [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metod. Det lägger till alla dessa rader på kartan för att rendera vägen. Mer information finns i avsnittet om att [lägga till en rad på kartan](./map-add-shape.md#addALine).

## <a name="query-the-route-via-xmlhttprequest"></a>Fråga route via XMLHttpRequest

<iframe height='500' scrolling='no' title='Visa riktningar från A till B på en karta' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>visar riktningar från A till B på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar och lägger till PIN-koder på kartan för att representera start- och slutpunkt för rutten. Du kan se [lägga till en PIN-kod på kartan](map-add-pin.md) anvisningar om hur du använder [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Det tredje kodblocket använder [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funktion i klassen kartan för att ställa in avgränsningsruta för kartan baserat på start- och slutpunkt för rutten.

Det fjärde kodblocket skickar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [API: T för Azure Maps-väg](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Senaste kodblocket tolkar det inkommande svaret. För ett lyckat svar samlar det in information om latitud och longitud för varje waypoint. En matris med rader skapas genom att ansluta varje waypoint till dess efterföljande waypoint. Det lägger till alla dessa rader på kartan för att rendera vägen. Du kan se [lägger till en rad på kartan](./map-add-shape.md#addALine) anvisningar.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se följande artiklar för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Visa trafik på kartan](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagera med kartan - mushändelser](./map-events.md)
