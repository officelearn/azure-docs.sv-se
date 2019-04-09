---
title: Visa sökresultat med Azure Maps | Microsoft Docs
description: Hur du utför en sökbegäran med Azure Maps och sedan visa resultaten på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be01c9d96386804b8bc074d81041104cbf592df6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271602"
---
# <a name="show-search-results-on-the-map"></a>Visa sökresultat på kartan

Den här artikeln visar hur du söker efter plats i närheten och visa sökresultatet på kartan.

Det finns två sätt att söka efter en plats i närheten. Ett sätt är att använda en tjänst-modul för att göra en sökbegäran. Det andra sättet är att göra en sökbegäran till [Azure Maps Fuzzy-sökning API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) via den [hämta API](https://fetch.spec.whatwg.org/). Båda metoderna beskrivs nedan.

## <a name="make-a-search-request-via-service-module"></a>Gör en sökbegäran via tjänstemodulen

<iframe height='500' scrolling='no' title='Visa sökresultat på en karta (Tjänstemodulen)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>visa sökresultat på en karta (Tjänstemodulen)</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket konstruerar ett Kartobjekt och ställer in autentiseringsmekanismen ska kunna använda nyckeln prenumeration. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar en `SubscriptionKeyCredentialPolicy` att autentisera HTTP-förfrågningar till Azure Maps med prenumerationsnyckeln. Sedan `atlas.service.MapsURL.newPipeline()` tar den `SubscriptionKeyCredential` princip och skapar en [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instans. Den `searchURL` representerar en URL för Azure Maps [Search](https://docs.microsoft.com/rest/api/maps/search) åtgärder.

Det tredje kodblocket skapar en datakälla objekt med den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klassen och Lägg till sökresultat. En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan.  En symbol layer skapas och datakällan har lagts till symbol-lager, som sedan läggs till i kartan.

Fjärde kodblocket använder den [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) -metod i den [tjänstemodulen](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). Du kan utföra en fri form textsökning via den [hämta Fuzzy Search rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) att söka efter orienteringspunkt. Fuzzy API för Get-sökning kan hantera valfri kombination av fuzzy indata. En samling med GeoJSON-funktionen från svaret extraheras sedan med hjälp av den `geojson.getFeatures()` metoden och läggs till datakällan, vilket gör automatiskt de data som renderas på kartan via symbol-lagret.

Senaste kodblocket justerar kamera gränser för kartan med hjälp av kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) egenskapen.

Sökningen begära, datakällan, och symbolen lager och kamera gränser skapas och Ställ in i kartan är klar [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) att säkerställa att resultaten visas när kartan har lästs in helt.


## <a name="make-a-search-request-via-fetch-api"></a>Gör en sökbegäran via hämta API

<iframe height='500' scrolling='no' title='Visa sökresultat på en karta' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>visa sökresultat på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket konstruerar ett Kartobjekt och ställer in autentiseringsmekanismen ska kunna använda nyckeln prenumeration. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar en URL för att göra en sökbegäran till. Det skapar också två matriser för att lagra gränsen och PIN-koder för sökresultat.

Det tredje kodblocket använder den [hämta API](https://fetch.spec.whatwg.org/) och gör en begäran att [Azure Maps Fuzzy-sökning API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) att söka efter Orienteringspunkter. Fuzzy-sökning API: et kan hantera valfri kombination av fuzzy indata. Den sedan hanterar och Parsar search-svar och lägger till resultatet PIN-koder till searchPins-matris.

Det fjärde kodblocket skapar en datakälla objekt med den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klassen och Lägg till sökresultat. En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. En symbol layer skapas och datakällan har lagts till symbol-lager, som sedan läggs till i kartan.

Det sista blocket kod skapar en [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) objekt med ett antal resultat och justerar kamera gränser för kartan med hjälp av kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Det återges sedan resultatet PIN-koder.

Sökbegäran, datakällan, och symbolen lager och kamera intervallet anges i kartans [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) att säkerställa att resultaten visas när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

Läs mer om **Fuzzy-sökning**:

> [!div class="nextstepaction"]
> [Azure Maps-API: T för Fuzzy Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se följande artiklar för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Hämta information från en koordinat](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Visa anvisningar från A till B](./map-route.md)
