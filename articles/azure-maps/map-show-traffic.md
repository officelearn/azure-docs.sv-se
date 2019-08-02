---
title: Visa trafik med Azure Maps | Microsoft Docs
description: Visa trafik data på en JavaScript-mappning
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 55bfc434082b2d5b7de193e969fc34f710657cdb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638626"
---
# <a name="show-traffic-on-the-map"></a>Visa trafik på kartan

Den här artikeln visar hur du visar trafik-och incident information på kartan.

## <a name="understand-the-code"></a>Förstå koden

<iframe height='456' scrolling='no' title='Visa trafik på en karta' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Visa trafik på en karta</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar det första blocket kod ett kart objekt. Du kan se [skapa en karta](map-create.md) för instruktioner.

Det andra blocket kod använder funktionen [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) i mappningens [händelse avlyssnings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att återge trafikflödena och incidenterna på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Kod exempel sida](https://aka.ms/AzureMapsSamples)

Förbättra användar upplevelsen:

> [!div class="nextstepaction"]
> [Mappa interaktion med mus händelser](./map-events.md)

> [!div class="nextstepaction"]
> [Skapa en tillgänglig karta](./map-accessibility.md)
