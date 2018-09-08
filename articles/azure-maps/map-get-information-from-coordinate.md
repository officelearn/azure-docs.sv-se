---
title: Visa information om en koordinat med Azure Maps | Microsoft Docs
description: Hur du vill visa information om en adress på kartan när en användare väljer en koordinat
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d1baa4adc555e65c4a25928d19f201dba6109142
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157692"
---
# <a name="get-information-from-a-coordinate"></a>Hämta information från en koordinat

Den här artikeln visar hur du gör en adress för omvänd sökning och vid en musklickning visa adressen för ställe i ett popup-fönster.

Det finns två sätt att göra en adress för omvänd sökning, en är genom att fråga den [Azure Maps omvänd adress Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) via en tjänst som modulen och den andra är genom att göra en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till API för att fråga den adress. Vi går igenom båda nedan.

## <a name="use-the-service-module-to-make-a-reverse-address-search"></a>Använda service-modulen för att göra en adress för omvänd sökning

### <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Hämta information från en koordinat (Tjänstemodulen)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>hämta information från en koordinat (Tjänstemodulen)</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Raden i det andra kodblocket skapar en instans av en tjänsteklient.

Det tredje kodblocket uppdaterar formatet för pekaren till en pekare.

Fjärde kodblocket skapar ett popup-fönster. Du kan se [lägga till ett popup-fönster på kartan](./map-add-popup.md) anvisningar.

Det sista blocket kod lägger till en händelselyssnare för musklick. Vid en musklickning skapas en sökfråga med koordinater punktens klickade på. Så används på kartan [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) slutpunkt för att fråga om adressen för koordinaterna.

Den samlar in adressen till ställe för ett lyckat svar och definierar popup-fönstret innehåll och position via [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funktion i klassen popup-fönstret.

## <a name="use-xmlhttprequest-to-make-a-reverse-address-search"></a>Använd XMLHTTPRequest för att göra en adress för omvänd sökning

### <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Hämta information från en koordinat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>hämta information från en koordinat</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket uppdaterar formatet för pekaren till en pekare.

Det tredje kodblocket skapar ett popup-fönster. Du kan se [lägga till ett popup-fönster på kartan](./map-add-popup.md) anvisningar.

Senaste kodblocket lägger till en händelselyssnare för musklick. Vid Klicka med musen, skickar den ett [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [Azure Maps omvänd adress Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Den samlar in adressen till ställe för ett lyckat svar och definierar popup-fönstret innehåll och position via [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funktion i klassen popup-fönstret

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [Adress för omvänd sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Öppna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Stäng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar:
* [Visa riktningar från A till B](./map-route.md)
* [Visa trafik](./map-show-traffic.md)
