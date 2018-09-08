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
ms.openlocfilehash: 37323bacf47613c0faf7769701808ecef2645115
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157961"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B 

Den här artikeln visar hur du gör en begäran om väg och visa rutten på kartan.

Det finns två sätt att göra det, ett sätt är av genom att fråga den [API: T för Azure Maps-väg](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) via en tjänst som modulen och den andra är genom att göra en [XMLHttpRequest](https://xhr.spec.whatwg.org/) -API: et. Båda beskrivs nedan.

## <a name="use-service-module-to-query-for-a-route"></a>Använda servicemodulen till frågan för en väg

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Visa riktningar från A till B på en karta (Tjänstemodulen)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/RBZbep/'>visar riktningar från A till B på en karta (Tjänstemodulen)</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Raden i det andra kodblocket skapar en instans av en tjänsteklient.

Det tredje kodblocket initierar den [rad sträng Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) på kartan.

Det fjärde kodblocket skapar och lägger till PIN-koder på kartan för att representera start- och slutpunkt för rutten. Du kan se [lägga till en PIN-kod på kartan](map-add-pin.md) anvisningar.

Nästa kodblock i använder [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funktion i klassen kartan för att ställa in avgränsningsruta för kartan baserat på start- och slutpunkt för rutten.

Det sjätte kodblocket skapas en väg.

Senaste kodblocket frågar Azure Maps-tjänst för händelsedirigering via den [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metod för att hämta en väg mellan start- och mål. Svaret parsas sedan till GeoJSON-format med den [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metod. Det lägger till alla dessa rader på kartan för att rendera vägen. Du kan se [lägger till en rad på kartan](./map-add-shape.md#addALine) för mer information.

## <a name="use-xmlhttprequest-to-query-for-a-route"></a>Använda XMLHTTPRequest till frågan för en väg

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Visa riktningar från A till B på en karta' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>visar riktningar från A till B på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar och lägger till PIN-koder på kartan för att representera start- och slutpunkt för rutten. Du kan se [lägga till en PIN-kod på kartan](map-add-pin.md) anvisningar.

Det tredje kodblocket använder [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funktion i klassen kartan för att ställa in avgränsningsruta för kartan baserat på start- och slutpunkt för rutten.

Det fjärde kodblocket skickar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [API: T för Azure Maps-väg](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Senaste kodblocket tolkar det inkommande svaret. För ett lyckat svar samlar det in information om latitud och longitud för varje waypoint. En matris med rader skapas genom att ansluta varje waypoint till dess efterföljande waypoint. Det lägger till alla dessa rader på kartan för att rendera vägen. Du kan se [lägger till en rad på kartan](./map-add-shape.md#addALine) anvisningar.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [Sträng Linjeskikt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar: 
* [Visa trafik på kartan](./map-show-traffic.md)
* [Interagera med kartan - mushändelser](./map-events.md)
