---
title: Visa vägbeskrivningar på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du visar vägbeskrivningar mellan två platser på en karta med Hjälp av Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: dde9264d0cb65726b624b918982cfa01985b63ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371415"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B

Den här artikeln visar hur du gör en ruttbegäran och visar rutten på kartan.

Det finns två sätt att göra det på. Det första sättet är att fråga [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) via en tjänstmodul. Det andra sättet är att använda [Hämta API](https://fetch.spec.whatwg.org/) för att göra en sökbegäran till Azure Maps [Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Båda sätten diskuteras nedan.

## <a name="query-the-route-via-service-module"></a>Fråga vägen via tjänstmodulen

<iframe height='500' scrolling='no' title='Visa vägbeskrivningar från A till B på en karta (ServiceModul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/RBZbep/'>pen show directions from A till B on a map (Service Module)</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket ett kartobjekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra kodblocket `TokenCredential` skapar en för att autentisera HTTP-begäranden till Azure Maps med åtkomsttoken. Den skickar `TokenCredential` sedan `atlas.service.MapsURL.newPipeline()` till och skapar en [Pipeline-instans.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Representerar `routeURL` en URL till Azure Maps [Route-åtgärder.](https://docs.microsoft.com/rest/api/maps/route)

Det tredje kodblocket skapar och lägger till ett [DataSource-objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan.

Det fjärde kodblocket skapar start- och [slutpunktersobjekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) och lägger till dem i dataSource-objektet.

En rad är en [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) för LineString. Ett [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) återger linjeobjekt som är förpackade i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som rader på kartan. Det fjärde kodblocket skapar och lägger till ett linjelager på kartan. Visa egenskaper för ett linjelager vid [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Ett [symbollager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder texter eller ikoner för att återge punktbaserade data som är förpackade i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Texterna eller ikonerna återges som symboler på kartan. Det femte kodblocket skapar och lägger till ett symbollager på kartan.

Det sjätte kodblocket frågar routningstjänsten Azure Maps, som ingår i [tjänstmodulen](how-to-use-services-module.md). Metoden [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) för RouteURL används för att hämta en rutt mellan start- och slutpunkterna. En GeoJSON-funktionssamling från svaret extraheras sedan med `geojson.getFeatures()` hjälp av metoden och läggs till i datakällan. Det återger sedan svaret som en rutt på kartan. Mer information om hur du lägger till en linje på kartan finns [i Lägga till en rad på kartan](map-add-line-layer.md).

Det sista kodblocket anger kartans gränser med hjälp av kartans [setCamera-egenskap.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)

Flödesfrågan, datakällan, symbolen, linjelagren och kamera bounds skapas inuti [händelseavlyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Den här kodstrukturen säkerställer att resultaten visas först när kartan har laddats helt.

## <a name="query-the-route-via-fetch-api"></a>Fråga vägen via Hämta API

<iframe height='500' scrolling='no' title='Visa vägbeskrivningar från A till B på en karta' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>pen show directions från A till B på en karta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första kodblocket ett kartobjekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra kodblocket skapar och lägger till ett [DataSource-objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan.

Det tredje kodblocket skapar start- och målpunkter för rutten. Sedan lägger den till dem i datakällan. Du kan se [lägga till en pin på kartan](map-add-pin.md) för instruktioner om hur du använder [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

Ett [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) återger linjeobjekt som är förpackade i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som rader på kartan. Det fjärde kodblocket skapar och lägger till ett linjelager på kartan. Visa egenskaper för ett linjelager vid [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Ett [symbollager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att återge punktbaserade data som är indelat i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. Det femte kodblocket skapar och lägger till ett symbollager på kartan. Visa egenskaper för ett symbollager på [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Nästa kodblock skapar `SouthWest` `NorthEast` och pekar från start- och målpunkterna och anger gränserna för kartan med hjälp av kartans [setCamera-egenskap.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)

Det sista kodblocket använder [Hämta API](https://fetch.spec.whatwg.org/) för att göra en sökbegäran till Azure Maps [Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Svaret tolkas sedan. Om svaret lyckades används latitud- och longitudinformationen för att skapa en matris en linje genom att ansluta dessa punkter. Raddata läggs sedan till i datakällan för att återge rutten på kartan. Du kan se [lägga till en rad på kartan](map-add-line-layer.md) för instruktioner.

Flödesfrågan, datakällan, symbolen, linjelagren och kamera bounds skapas inuti [händelseavlyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Återigen vill vi se till att resultaten visas efter att kartan laddas helt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för användning av routningstjänsten](how-to-use-best-practices-for-search.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se följande artiklar för fullständiga kodexempel:

> [!div class="nextstepaction"]
> [Visa trafik på kartan](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagera med kartan - mushändelser](./map-events.md)
