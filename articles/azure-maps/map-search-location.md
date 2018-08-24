---
title: Visa sökresultat med Azure Maps | Microsoft Docs
description: Hur du utför en sökbegäran med Azure Maps och sedan visa resultaten på en Javascrip karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b0ab271eab45a6f4b05d01713e2e2ddd22a22ea3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746611"
---
# <a name="show-search-results-on-the-map"></a>Visa sökresultat på kartan

Den här artikeln visar hur du gör en sökbegäran och visa sökresultatet på kartan. 

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Visa sökresultat på en karta' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>visa sökresultat på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar och lägger till ett lager med search PIN-koder på kartan. Du kan se [lägga till en PIN-kod på kartan](./map-add-pin.md) anvisningar.

Det tredje kodblocket skickar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Senaste kodblocket tolkar det inkommande svaret. För ett lyckat svar samlar det in latitud och longitud information för varje plats som returneras. Den lägger till alla plats punkter på kartan som PIN-koder och justerar gränserna för kartan för att rendera alla PIN-koder.


## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 

* [Azure Maps-API: T för Fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar: 
* [Hämta information från en koordinat](./map-get-information-from-coordinate.md)
* [Visa riktningar från A till B](./map-route.md)
