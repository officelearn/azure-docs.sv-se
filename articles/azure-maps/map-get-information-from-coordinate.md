---
title: Visar information om en koordinat med Azure Maps | Microsoft Docs
description: Så här visar du information om en adress på kartan när en användare väljer en koordinat
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3caae47f7f8f5f9c917e3a59513e6cd33cdcaeae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600501"
---
# <a name="get-information-from-a-coordinate"></a>Hämta information från en koordinat

Den här artikeln visar hur du gör en adress för omvänd sökning och visa adressen för ställe vid en musklickning i ett popup-fönster. 

## <a name="understand-the-code"></a>Förstå koden

<iframe height='500' scrolling='no' title='Hämta information från en koordinat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>hämta information från en koordinat</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket uppdaterar formatet för markören till en pekare.

Det tredje kodblocket skapar ett popup-fönster. Du kan se [lägga till ett popup-fönster på kartan](./map-add-popup.md) anvisningar.

Senaste kodblocket lägger till en händelselyssnaren för musklickningar. Vid en musklickning skickar den ett [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [Azure Maps omvänd adress Sök API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Den samlar in adressen till ställe för ett lyckat svar, och definierar popup-innehåll och placeringen via [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funktion av popup-klass

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [Adressen för omvänd sökning](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [karta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup-fönster](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Öppna](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Stäng](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Att lägga till i din maps mer kodexempel finns i följande artiklar: 
* [Visa anvisningar från A till B](./map-route.md)
* [Visa trafik](./map-show-traffic.md)
