---
title: Visa information om en koordinat med Azure Maps | Microsoft Docs
description: Hur du vill visa information om en adress på kartan när en användare väljer en koordinat
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 20616adf649924a13e80411aa5135889a175f442
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750196"
---
# <a name="get-information-from-a-coordinate"></a>Hämta information från en koordinat

Den här artikeln visar hur du gör en sökning i omvänd adress som visar adressen för en plats för klickade på popup-fönstret.

Det finns två sätt att göra en adress för omvänd sökning. Ett sätt är att fråga den [Azure Maps omvänd adress Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) via en tjänst-modul. Det andra sättet är att göra en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till API för att hitta en adress. Båda sätten övervakas nedan.

## <a name="make-a-reverse-search-request-via-service-module"></a>Gör en begäran om omvänd sökning via tjänstemodulen

<iframe height='500' scrolling='no' title='Hämta information från en koordinat (Tjänstemodulen)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>hämta information från en koordinat (Tjänstemodulen)</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Raden i det andra kodblocket skapar en instans av en klienttjänst.

Det tredje kodblocket uppdaterar formatet för pekaren till en pekare och [popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objekt. Du kan se [lägga till ett popup-fönster på kartan](./map-add-popup.md) anvisningar.

Fjärde blockeringen av kod lägger till en [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för mus klickar. Vid en musklickning skapas en sökfråga med koordinaterna punktens klickade på. Därefter använder kartans [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest) slutpunkt för att fråga om adressen för koordinaterna.

Den samlar in adressen till ställe för ett lyckat svar och definierar popup-fönstret innehåll och position via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funktion i klassen popup-fönstret.

Ändringen av markören ett popup-objekt och click-händelse skapas i kartans [belastningen händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) att säkerställa att kartan belastningar fullständigt innan koordinater informationen kan hämtas.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Gör en begäran om omvänd sökning via XMLHttpRequest

<iframe height='500' scrolling='no' title='Hämta information från en koordinat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>hämta information från en koordinat</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första blocket av koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket uppdaterar formatet för pekaren till en pekare och [popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objekt. Du kan se [lägga till ett popup-fönster på kartan](./map-add-popup.md) anvisningar.

Det tredje kodblocket lägger till en händelselyssnare för musklick. Vid Klicka med musen, skickar den ett [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [Azure Maps omvänd adress Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) att fråga efter klickade på koordinater-adress. Den samlar in adressen till ställe för ett lyckat svar och definierar popup-fönstret innehåll och position via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funktion i klassen popup-fönstret.

Ändringen av markören ett popup-objekt och click-händelse skapas i kartans [belastningen händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) att säkerställa att kartan belastningar fullständigt innan koordinater informationen kan hämtas.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Se följande artiklar för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Visa riktningar från A till B](./map-route.md)

> [!div class="nextstepaction"]
> [Visa trafik](./map-show-traffic.md)
