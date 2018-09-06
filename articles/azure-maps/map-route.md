---
title: Visa riktningar med Azure Maps | Microsoft Docs
description: Så här visar du riktningarna mellan två platser på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 80abd6db9888524aa6a66d9861d8dc2d05188e19
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781504"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B 

Den här artikeln visar hur du gör en begäran om väg och visa rutten på kartan. 

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
