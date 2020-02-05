---
title: Visa väg riktningar på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du visar vägvisningar mellan två platser på en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 359f2b42ca6f56087be53a5aeb328fe43a478d63
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988299"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B

Den här artikeln visar hur du gör en cirkulations förfrågan och visar vägen på kartan.

Det finns två sätt att göra detta på. Det första sättet är att fråga [Azure Maps Route-API: et](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) via en service-modul. Det andra sättet är att använda [hämtnings-API: et](https://fetch.spec.whatwg.org/) för att göra en Sök förfrågan till [Azure Maps Route-API: et](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Båda sätten beskrivs nedan.

## <a name="query-the-route-via-service-module"></a>Fråga vägen via Service-modulen

<iframe height='500' scrolling='no' title='Visa vägvisningar från A till B på en karta (service Module)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se anvisningarna för Penn <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Visa från a till B på en karta (service Module)</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I ovanstående kod konstruerar det första blocket ett kart objekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod skapar ett `TokenCredential` för att autentisera HTTP-förfrågningar som ska Azure Maps med åtkomsttoken. Den skickar sedan `TokenCredential` till `atlas.service.MapsURL.newPipeline()` och skapar en [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) -instans. `routeURL` representerar en URL för att Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) -åtgärder.

Det tredje blocket med kod skapar och lägger till ett [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -objekt till kartan.

Det fjärde blocket Code skapar start-och slut [punkts](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objekt och lägger till dem i DataSource-objektet.

En linje är en [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) för lin Est ring. En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) återger rad objekt i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som rader på kartan. Det fjärde blocket med kod skapar och lägger till ett linje skikt i kartan. Se egenskaperna för ett linje lager på [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Ett [symbol lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att återge punktbaserade data i data [källan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Texten eller ikonerna återges som symboler på kartan. Det femte blocket med kod skapar och lägger till ett symbol lager till kartan.

Det sjätte blocket med kod frågar Azure Maps Routningstjänst, som är en del av [Service-modulen](how-to-use-services-module.md). [CalculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) -metoden för RouteURL används för att hämta en väg mellan start-och slut punkterna. En samling av en interjson-funktion från svaret extraheras sedan med hjälp av metoden `geojson.getFeatures()` och läggs till i data källan. Sedan återges svaret som en väg på kartan. Mer information om hur du lägger till en linje till kartan finns i [lägga till en rad på kartan](map-add-line-layer.md).

Det sista blocket i koden anger gränserna för kartan med kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) -egenskap.

Flödes frågan, data källan, symbolen, rad skikten och kamerans gränser skapas och anges i kartans [händelse lyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Den här kod strukturen säkerställer att resultaten visas först när kartan har lästs in helt.

## <a name="query-the-route-via-fetch-api"></a>Fråga vägen via hämtnings-API

<iframe height='500' scrolling='no' title='Visa vägvisningar från A till B på en karta' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>anvisningarna från a till B på en karta genom att</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket kod ett kart objekt och ställer in autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod skapar och lägger till ett [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -objekt till kartan.

Det tredje kod blocket skapar start-och mål platserna för vägen och lägger till dem i data källan. Du kan se [Lägg till en PIN-kod på kartan](map-add-pin.md) för instruktioner om hur du använder [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) återger rad objekt i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som rader på kartan. Det fjärde blocket med kod skapar och lägger till ett linje skikt i kartan. Se egenskaperna för ett linje lager på [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Ett [symbol lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att återge punktbaserade data i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. Det femte blocket med kod skapar och lägger till ett symbol lager till kartan. Se egenskaperna för ett symbol lager på [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Nästa kodblock skapar `SouthWest` och `NorthEast` punkter från start-och mål punkterna och anger kartans gränser med kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) -egenskap.

Det sista blocket i koden använder [hämtnings-API: t](https://fetch.spec.whatwg.org/) för att göra en Sök förfrågan till [Azure Maps Route-API: et](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Svaret parsas sedan. Om svaret lyckades används informationen om latitud och longitud för att skapa en matris med en linje genom att ansluta dessa punkter. Linje data läggs sedan till i data källan för att rendera vägen på kartan. Du kan se [Lägg till en rad på kartan](map-add-line-layer.md) för instruktioner.

Flödes frågan, data källan, symbolen, rad skikten och kamerans gränser skapas och anges i kartans [händelse lyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Återigen vill vi se till att resultaten visas när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Visa trafik på kartan](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagera med kart mus händelser](./map-events.md)
