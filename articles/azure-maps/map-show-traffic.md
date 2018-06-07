---
title: Visa trafik med Azure Maps | Microsoft Docs
description: Så här visar du trafikinformation på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6ff7a0270509c244fc97bd04d8ba648fd262dc58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600127"
---
# <a name="show-traffic-on-the-map"></a>Visa trafik på kartan

Den här artikeln visar hur du vill visa information om trafik och händelser på kartan. 

## <a name="understand-the-code"></a>Förstå koden

<iframe height='456' scrolling='no' title='Visa trafik på en karta' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Visa trafik på en karta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](map-create.md) anvisningar.

Andra kodblocket använder [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) funktion i kartan klassen för att återge trafikflöde och incidenter på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [karta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic)

Att lägga till i din maps mer kodexempel finns i följande artiklar: 
* [Interaktion med kartan – musen händelser](./map-events.md)
* [Skapa en tillgänglig mappning](./map-accessibility.md)

Kolla in våra [exempel teckentabellen](http://aka.ms/AzureMapsSamples) för scenarier med flera mappning.
