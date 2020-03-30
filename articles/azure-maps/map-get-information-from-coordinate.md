---
title: Visa information om en koordinat på en karta | Microsoft Azure Maps
description: Läs om hur du visar information om en adress på kartan när en användare väljer en koordinat.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371426"
---
# <a name="get-information-from-a-coordinate"></a>Hämta information från en koordinat

Den här artikeln visar hur du gör en omvänd adresssökning som visar adressen till en klickad popup-plats.

Det finns två sätt att göra en omvänd adresssökning. Ett sätt är att fråga [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) via en tjänstmodul. Det andra sättet är att använda [Hämta API](https://fetch.spec.whatwg.org/) för att göra en begäran till Azure Maps Reverse Address [Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för att hitta en adress. Båda sätten undersöks nedan.

## <a name="make-a-reverse-search-request-via-service-module"></a>Gör en omvänd sökbegäran via servicemodul

<iframe height='500' scrolling='no' title='Hämta information från en koordinat (servicemodul)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pen hämta <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>information från en koordinat (Service Module)</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket ett kartobjekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra kodblocket `TokenCredential` skapar en för att autentisera HTTP-begäranden till Azure Maps med åtkomsttoken. Den skickar `TokenCredential` sedan `atlas.service.MapsURL.newPipeline()` till och skapar en [Pipeline-instans.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Representerar `searchURL` en URL till Azure Maps [Search-åtgärder.](https://docs.microsoft.com/rest/api/maps/search)

Det tredje kodblocket uppdaterar stilen på muspekaren till en pekare och skapar ett [popup-objekt.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Du kan se [lägga till en popup på kartan](./map-add-popup.md) för instruktioner.

Det fjärde kodblocket lägger till en [musklicksavlyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). När den utlöses skapas en sökfråga med koordinaterna för den klickade punkten. Den använder sedan metoden [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)för att fråga [api:et Hämta sökadress reversera](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för adressen till koordinaterna. En GeoJSON-funktionssamling extraheras `geojson.getFeatures()` sedan med hjälp av metoden från svaret.

Det femte kodblocket ställer in HTML-popup-innehållet för att visa svarsadressen för den klickade koordinatpositionen.

Ändringen av markören, popup-objektet och klickhändelsen skapas alla i kartans [läshändelselyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Den här kodstrukturen säkerställer att kartan läses in helt innan koordinaterna hämtas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Gör en omvänd sökbegäran via Hämta API

Klicka på kartan för att göra en omvänd geokodbegäran för den platsen med hjälp av hämtning.

<iframe height='500' scrolling='no' title='Hämta information från en koordinat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen Hämta information från en<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>koordinat</a> av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första kodblocket ett kartobjekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra kodblocket uppdaterar muspekarens format till en pekare. Det instansierar ett [popup-objekt.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Du kan se [lägga till en popup på kartan](./map-add-popup.md) för instruktioner.

Det tredje kodblocket lägger till en händelseavlyssnare för musklick. Vid ett musklick används [hämtnings-API:et](https://fetch.spec.whatwg.org/) för att fråga [azure maps-sökfunktionen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för den klickade koordinatensadressen. För ett lyckat svar samlar den in adressen för den klickade platsen. Den definierar popup-innehåll och position med hjälp av [funktionen setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) för popup-klassen.

Ändringen av markören, popup-objektet och klickhändelsen skapas alla i kartans [läshändelselyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Den här kodstrukturen säkerställer att kartan läses in helt innan koordinaterna hämtas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för att använda söktjänsten](how-to-use-best-practices-for-search.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Se följande artiklar för fullständiga kodexempel:

> [!div class="nextstepaction"]
> [Visa anvisningar från A till B](./map-route.md)

> [!div class="nextstepaction"]
> [Visa trafik](./map-show-traffic.md)
