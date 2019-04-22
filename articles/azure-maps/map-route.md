---
title: Visa riktningar med Azure Maps | Microsoft Docs
description: Så här visar du riktningarna mellan två platser på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: b8205383c25ba04212126e0e6ca1bd44e4efad1a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264530"
---
# <a name="show-directions-from-a-to-b"></a>Visa anvisningar från A till B

Den här artikeln visar hur du gör en begäran om väg och visa rutten på kartan.

Det finns två sätt att göra detta. Det första sättet är att fråga den [API: T för Azure Maps-väg](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) via en tjänst-modul. Det andra sättet är att använda den [hämta API](https://fetch.spec.whatwg.org/) att göra en sökbegäran till den [API: T för Azure Maps-väg](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Båda metoderna beskrivs nedan.

## <a name="query-the-route-via-service-module"></a>Fråga route via tjänstemodulen

<iframe height='500' scrolling='no' title='Visa riktningar från A till B på en karta (Tjänstemodulen)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/RBZbep/'>visar riktningar från A till B på en karta (Tjänstemodulen)</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket konstruerar ett Kartobjekt och ställer in autentiseringsmekanismen att använda nyckeln prenumeration. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar en `SubscriptionKeyCredentialPolicy` att autentisera HTTP-förfrågningar till Azure Maps med prenumerationsnyckeln. Den `atlas.service.MapsURL.newPipeline()` tar den `SubscriptionKeyCredential` princip och skapar en [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instans. Den `routeURL` representerar en URL för Azure Maps [väg](https://docs.microsoft.com/rest/api/maps/route) åtgärder.

Tredje blockeringen av kod som skapar och lägger till en [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objekt på kartan.

Det fjärde kodblocket skapar start- och [punkter](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objekt och lägger till dem i datakällobjektet.

En rad är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) av LineString. En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderingar rad objekt och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som rader på kartan. Det fjärde kodblocket skapar och lägger till en linjeskikt på kartan. Se egenskaperna för en linjeskikt på [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. Det femte kodblocket skapar och lägger till en symbol-lager för kartan.

Det sjätte kodblocket frågar routningstjänsten Azure Maps, som är en del av den [tjänstemodulen](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). Den [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) -metoden för RouteURL används för att få en väg mellan start- och slutpunkter. En samling med GeoJSON-funktionen från svaret extraheras sedan med hjälp av den `geojson.getFeatures()` metoden och har lagts till datakällan. Det renderas sedan svaret som en väg på kartan. Läs mer om att lägga till en rad på kartan [lägger till en rad på kartan](./map-add-shape.md#addALine).

Senaste kodblocket anger gränser på kartan med hjälp av kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) egenskapen.

Väg frågan, datakällan, symbol och rad lager och kamera gränser skapas och ställa in i kartan [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) att säkerställa att resultaten visas när kartan har lästs in helt.

## <a name="query-the-route-via-fetch-api"></a>Fråga route via hämta API

<iframe height='500' scrolling='no' title='Visa riktningar från A till B på en karta' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>visar riktningar från A till B på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket konstruerar ett Kartobjekt och ställer in autentiseringsmekanismen att använda nyckeln prenumeration. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra blockeringen av kod som skapar och lägger till en [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objekt på kartan.

Tredje kodblocket skapar återställningspunkter för start- och mål för flödet och lägger till dem till datakällan. Du kan se [lägga till en PIN-kod på kartan](map-add-pin.md) anvisningar om hur du använder [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderingar rad objekt och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som rader på kartan. Det fjärde kodblocket skapar och lägger till en linjeskikt på kartan. Se egenskaperna för en linjeskikt på [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. Det femte kodblocket skapar och lägger till en symbol-lager för kartan. Se egenskaperna för en symbol-lager i [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Nästa kodblock skapar `SouthWest` och `NorthEast` pekar från start- och mål-punkter och anger det giltiga intervallet för kartan med hjälp av kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) egenskapen.

Det sista blocket kod använder den [hämta API](https://fetch.spec.whatwg.org/) att göra en sökbegäran till den [API: T för Azure Maps-väg](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Svaret är sedan parsas. Om svaret används latitud och longitud informationen till att skapa en matris till en linje genom att ansluta dessa punkter. Raddata läggs sedan till datakällan för att rendera vägen på kartan. Du kan se [lägger till en rad på kartan](./map-add-shape.md#addALine) anvisningar.

Väg frågan, datakällan, symbol och rad lager och kamera gränser skapas och ställa in i kartan [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) att säkerställa att resultaten visas när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se följande artiklar för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Visa trafik på kartan](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interagera med kartan - mushändelser](./map-events.md)
