---
title: Visa information om en koordinat med Azure Maps | Microsoft Docs
description: Hur du vill visa information om en adress på kartan när en användare väljer en koordinat
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256064"
---
# <a name="get-information-from-a-coordinate"></a>Hämta information från en koordinat

Den här artikeln visar hur du gör en sökning i omvänd adress som visar adressen för en plats för klickade på popup-fönstret.

Det finns två sätt att göra en adress för omvänd sökning. Ett sätt är att fråga den [Azure Maps omvänd adress Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) via en tjänst-modul. Det andra sättet är att använda den [hämta API](https://fetch.spec.whatwg.org/) och gör en begäran till den [Azure Maps omvänd adress Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) hitta en adress. Båda sätten övervakas nedan.

## <a name="make-a-reverse-search-request-via-service-module"></a>Gör en begäran om omvänd sökning via tjänstemodulen

<iframe height='500' scrolling='no' title='Hämta information från en koordinat (Tjänstemodulen)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>hämta information från en koordinat (Tjänstemodulen)</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket konstruerar ett Kartobjekt och ställer in autentiseringsmekanismen ska kunna använda nyckeln prenumeration. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar en `SubscriptionKeyCredentialPolicy` att autentisera HTTP-förfrågningar till Azure Maps med prenumerationsnyckeln. Sedan `atlas.service.MapsURL.newPipeline()` tar den `SubscriptionKeyCredential` princip och skapar en [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instans. Den `searchURL` representerar en URL för Azure Maps [Search](https://docs.microsoft.com/rest/api/maps/search) åtgärder.

Det tredje kodblocket uppdaterar formatet för pekaren till en pekare och skapar en [popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objekt. Du kan se [lägga till ett popup-fönster på kartan](./map-add-popup.md) anvisningar.

Fjärde blockeringen av kod lägger till en musklickning [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). När det utlöses, skapar en sökfråga med koordinaterna punktens klickade på. Använder sedan servicemodulen [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) metod för att fråga den [hämta adress omvänt API: et Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för koordinaterna-adress. En samling med GeoJSON-funktionen från svaret extraheras sedan med hjälp av den `geojson.getFeatures()` metoden.

Det femte kodblocket ställer in HTML-popup-fönstret innehåll att visa svarsadress för klickade på koordinaten positionen.

Ändringen av markören ett popup-objekt och click-händelse skapas i kartans [belastningen händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) att säkerställa att kartan belastningar fullständigt innan koordinater informationen kan hämtas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Gör en begäran om omvänd sökning via hämta API

Klicka på kartan för att göra en omvänd geocode-begäran för den platsen med hjälp av hämtning.

<iframe height='500' scrolling='no' title='Hämta information från en koordinat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>hämta information från en koordinat</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket konstruerar ett Kartobjekt och ställer in autentiseringsmekanismen ska kunna använda nyckeln prenumeration. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket uppdaterar formatet för pekaren till en pekare och [popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objekt. Du kan se [lägga till ett popup-fönster på kartan](./map-add-popup.md) anvisningar.

Det tredje kodblocket lägger till en händelselyssnare för musklick. Vid en musklickning använder den [hämta API](https://fetch.spec.whatwg.org/) att fråga den [Azure Maps omvänd adress Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för klickade på koordinater-adress. Den samlar in adressen till ställe för ett lyckat svar och definierar popup-fönstret innehåll och position via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funktion i klassen popup-fönstret.

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
