---
title: Hantera mushändelser med Azure Maps | Microsoft Docs
description: Hur du gör en interaktiv Javascript-karta med kartan händelser
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b9174d98dd6d4dfb5353d6976d074bb4c91373dc
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678329"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagera med kartan - mushändelser

Den här artikeln visar hur du använder [mappa klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [händelser](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) egenskapen att markera händelser på kartan och på olika lager i kartan. Den också visar hur du använder egenskapen kartan klass händelser för att markera händelser när du interagerar med en HTML-markör.

## <a name="interact-with-the-map"></a>Interagera med kartan

<iframe height='600' scrolling='no' title='Interagera med kartan – mushändelser' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interagera med kartan – mushändelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Spela upp med kartan ovan och se de motsvarande mushändelser som markerats till höger. Du kan klicka på den **JS fliken** att visa och redigera JavaScript-koden. Du kan också klicka på den **Redigera på CodePen** knappen och redigera kod på CodePen.

## <a name="interact-with-map-layers"></a>Interagera med kartskikt

<iframe height='600' scrolling='no' title='Interagera med kartan – lager-händelser' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagera med kartan – Layer händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan visar namnet på de händelser som hämta utlösta upp när du interagerar med Symbol-lagret. Alla symbolen, bubbeldiagram, rad och Polygon lagret stöd för samma uppsättning händelser. Den Bildrutsskikt stöder inte någon av dessa händelser.

## <a name="interact-with-html-marker"></a>Interagera med HTML markör

<iframe height='500' scrolling='no' title='Interagera med kartan - HTML markör händelser' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interagera med kartan - HTML markör händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan lägger till Javascript map händelser till en HTML-markör. Den visar även namnet på de händelser som hämta utlösta upp när du interagerar med HTML-markör.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se följande artiklar för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Visa sökresultat](./map-search-location.md)

> [!div class="nextstepaction"]
> [Exempelsida för kod](https://aka.ms/AzureMapsSamples)