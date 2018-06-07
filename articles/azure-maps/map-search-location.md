---
title: Visa sökresultat med Azure Maps | Microsoft Docs
description: Hur du utför en sökbegäran med Azure Maps och sedan visa resultaten på en Javascrip-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b77737b16b23ed00c8f12f84e6a8558a665a7d15
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599889"
---
# <a name="show-search-results-on-the-map"></a>Visa sökresultat på kartan

Den här artikeln visar hur du gör en sökbegäran och visa sökresultat på kartan. 

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Visa sökresultat på en karta' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>visa sökresultat på en karta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar och lägger till ett lager av sökning PIN på kartan. Du kan se [lägga till en PIN-kod på kartan](./map-add-pin.md) anvisningar.

Det tredje kodblocket skickar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [Azure Maps Fuzzy Sök API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Senaste kodblocket tolkar inkommande svaret. För ett lyckat svar samlar det in latitud och longitud information för varje plats returneras. Det lägger till plats pekar på kartan som PIN-koder och justerar gränserna för kartan för att återge alla PIN-koder.


## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 

* [Azure mappar Fuzzy Sök API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [karta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    
Att lägga till i din maps mer kodexempel finns i följande artiklar: 
* [Hämta information från en koordinat](./map-get-information-from-coordinate.md)
* [Visa anvisningar från A till B](./map-route.md)
