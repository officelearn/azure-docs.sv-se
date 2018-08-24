---
title: Visa information om en koordinat med Azure Maps | Microsoft Docs
description: Hur du vill visa information om en adress på kartan när en användare väljer en koordinat
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e1cbed8995c0efbfb6010daaca5cd97ebec92dc6
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746350"
---
# <a name="get-information-from-a-coordinate"></a>Hämta information från en koordinat

Den här artikeln visar hur du gör en adress för omvänd sökning och vid en musklickning visa adressen för ställe i ett popup-fönster. 

## <a name="understand-the-code"></a>Förstå koden

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
