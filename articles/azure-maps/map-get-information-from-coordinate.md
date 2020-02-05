---
title: Visa information om en koordinat på en karta | Microsoft Azure Maps
description: Lär dig hur du visar information om en adress på kartan när en användare väljer en koordinat.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0b1ab7b2c233eb1e6e231b0ae7935b6c24363948
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988540"
---
# <a name="get-information-from-a-coordinate"></a>Hämta information från en koordinat

Den här artikeln visar hur du gör en omvänd adresss ökning som visar adressen till en klickad popup-plats.

Det finns två sätt att göra en omvänd adresss ökning. Ett sätt är att fråga [Azure Maps API för omvänd adresss ökning](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) via en service-modul. Det andra sättet är att använda [hämtnings-API: t](https://fetch.spec.whatwg.org/) för att skicka en begäran till [Azure Maps omvänd Address Search-API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för att hitta en adress. Båda sätten undersökts nedan.

## <a name="make-a-reverse-search-request-via-service-module"></a>Gör en omvänd search-begäran via service module

<iframe height='500' scrolling='no' title='Hämta information från en koordinat (service Module)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Hämta information från en koordinat (service Module)</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket ett kart objekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra kod blocket skapar ett `TokenCredential` för att autentisera HTTP-förfrågningar som ska Azure Maps med åtkomsttoken. Den skickar sedan `TokenCredential` till `atlas.service.MapsURL.newPipeline()` och skapar en [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -instans. `searchURL` representerar en URL för att Azure Maps [Sök](https://docs.microsoft.com/rest/api/maps/search) åtgärder.

Det tredje kod blocket uppdaterar stilen för mus markören till en pekare och skapar ett [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) -objekt. Du kan se [Lägg till ett popup-fönster på kartan](./map-add-popup.md) för instruktioner.

Det fjärde blocket kod lägger till en mus klicknings [händelse lyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). När den utlöses skapas en Sök fråga med koordinaterna för den klickade punkten. Den använder sedan metoden [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)för att skicka frågor till kommandot [Get search Address reversed API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för adresserna för koordinaterna. En insamling av en interjson-funktion extraheras sedan med hjälp av metoden `geojson.getFeatures()` från svaret.

Det femte blocket med kod konfigurerar HTML-popup-innehållet för att Visa svars adressen för den klickade koordinatens position.

Ändringen av markör, ett popup-objekt och händelsen Klickning skapas i kartans [inläsnings händelse lyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att se till att mappningen läses in fullständigt innan koordinaternas information hämtas.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Gör en omvänd search-begäran via Hämta API

Klicka på kartan för att göra en omvänd polycode-begäran för den platsen med Hämta.

<iframe height='500' scrolling='no' title='Hämta information från en koordinat' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Hämta information från en koordinat</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket kod ett kart objekt och ställer in autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod uppdaterar stilen för mus markören till en visare. Den instansierar ett [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) -objekt. Du kan se [Lägg till ett popup-fönster på kartan](./map-add-popup.md) för instruktioner.

Det tredje blocket kod lägger till en händelse lyssnare för mus klickningar. När du klickar på musen används [hämtnings-API](https://fetch.spec.whatwg.org/) : t för att fråga [Azure Maps API för omvänd adresss ökning](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) för den klickade koordinatens adress. För ett lyckat svar samlar det in adressen för den klickade platsen. Den definierar popup-innehåll och placering med hjälp av [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) -funktionen i popup-klassen.

Ändringen av markör, ett popup-objekt och händelsen Klickning skapas i kartans [inläsnings händelse lyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att säkerställa att mappningen läses in fullständigt innan samordnar information hämtas.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Motta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Visa vägvisningar från A till B](./map-route.md)

> [!div class="nextstepaction"]
> [Visa trafik](./map-show-traffic.md)
