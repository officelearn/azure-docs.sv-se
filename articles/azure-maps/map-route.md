---
title: Visa riktningar med Azure Maps | Microsoft Docs
description: Så här visar du riktningarna mellan två platser på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 52462c1c5a2a1a9698a2b51708e63b1bb1664f6e
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745544"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B 

Den här artikeln visar hur du gör en begäran om väg och visa rutten på kartan. 

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

* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar: 
* [Visa trafik på kartan](./map-show-traffic.md)
* [Interagera med kartan - mushändelser](./map-events.md)
