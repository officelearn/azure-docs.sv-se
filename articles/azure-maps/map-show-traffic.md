---
title: Visa trafik med Azure Maps | Microsoft Docs
description: Hur du vill visa trafikdata på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 11/10/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 45f7913c5cc69f99b01ba1a911910273673856d3
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2018
ms.locfileid: "51513780"
---
# <a name="show-traffic-on-the-map"></a>Visa trafik på kartan

Den här artikeln visar hur du visar information om trafik och händelser på kartan.

## <a name="understand-the-code"></a>Förstå koden

<iframe height='456' scrolling='no' title='Visa trafik på en karta' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>visar trafik på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](map-create.md) anvisningar.

Andra kodblocket använder [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) funktion i kartans [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktionen att återge trafikflöden och incidenter på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se följande artiklar för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Exempelsida för kod](https://aka.ms/AzureMapsSamples)

Förbättra din användarupplevelsen:

> [!div class="nextstepaction"]
> [Mappa interaktion med musen händelser](./map-events.md)

> [!div class="nextstepaction"]
> [Att skapa en tillgänglig karta](./map-accessibility.md)
