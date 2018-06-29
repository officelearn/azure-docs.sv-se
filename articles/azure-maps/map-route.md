---
title: Visa anvisningar med Azure Maps | Microsoft Docs
description: Så här visar du riktningarna mellan två platser på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "35759801"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B 

Den här artikeln visar hur du gör en väg begäran och visa vägen på kartan. 

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Visa anvisningar från A till B på en karta' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>visa anvisningarna från A till B på en karta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar och lägger till PIN-koder på kartan för att representera start- och slutpunkt för vägen. Du kan se [lägga till en PIN-kod på kartan](map-add-pin.md) anvisningar.

Det tredje kodblocket använder [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) funktion i kartan klassen för att ange rutan kartans baserat på start- och slutpunkt för vägen.

Det fjärde kodblocket skickar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [Azure Maps väg API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Senaste kodblocket tolkar inkommande svaret. Den samlar in informationen för varje waypoint latitud och longitud för ett lyckat svar. En matris med rader skapas genom att ansluta varje waypoint till dess efterföljande waypoint. Det lägger till dessa rader till kartan ska renderas vägen. Du kan se [lägga till en rad på kartan](./map-add-shape.md#addALine) anvisningar.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 

* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

Att lägga till i din maps mer kodexempel finns i följande artiklar: 
* [Visa trafik på kartan](./map-show-traffic.md)
* [Interaktion med kartan - mushändelser](./map-events.md)
