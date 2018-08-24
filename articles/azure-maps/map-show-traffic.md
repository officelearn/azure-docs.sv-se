---
title: Visa trafik med Azure Maps | Microsoft Docs
description: Hur du vill visa trafikdata på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2499822db587dbf47dccedf6039d0fb5823c58b5
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746086"
---
# <a name="show-traffic-on-the-map"></a>Visa trafik på kartan

Den här artikeln visar hur du visar information om trafik och händelser på kartan. 

## <a name="understand-the-code"></a>Förstå koden

<iframe height='456' scrolling='no' title='Visa trafik på en karta' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>visar trafik på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](map-create.md) anvisningar.

Andra kodblocket använder [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) funktion i klassen kartan för att återge trafikflöden och incidenter på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic)

Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar: 
* [Interagera med kartan – mushändelser](./map-events.md)
* [Att skapa en tillgänglig karta](./map-accessibility.md)

Kolla in våra [exempel teckentabellen](http://aka.ms/AzureMapsSamples) för fler mappning scenarier.
