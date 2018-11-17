---
title: Visa sökresultat med Azure Maps | Microsoft Docs
description: Hur du utför en sökbegäran med Azure Maps och sedan visa resultaten på en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 031085b3048d0ffc92dd5a35b4054903088b4858
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824344"
---
# <a name="show-search-results-on-the-map"></a>Visa sökresultat på kartan

Den här artikeln visar hur du söker efter plats i närheten och visa sökresultatet på kartan.

Det finns två sätt att söka efter en plats i närheten. Ett sätt är att använda en tjänst-modul för att göra en sökbegäran. Det andra sättet är att göra en sökbegäran via en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [Azure Maps Fuzzy-sökning API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Båda metoderna beskrivs nedan.

## <a name="make-a-search-request-via-service-module"></a>Gör en sökbegäran via tjänstemodulen

<iframe height='500' scrolling='no' title='Visa sökresultat på en karta (Tjänstemodulen)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>visa sökresultat på en karta (Tjänstemodulen)</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan första kodblocket konstruerar ett Kartobjekt och initierar klienttjänsten. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket använder den [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-) -metod i den [tjänstemodulen](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). Du kan utföra en fri form textsökning via den [Fuzzy-sökning rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) att söka efter orienteringspunkt. Fuzzy-sökning API kan hantera valfri kombination av fuzzy indata. Svaret från tjänsten fuzzy-sökning parsas sedan till GeoJSON-format med den [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) metod. 

Det tredje kodblocket skapar en datakälla objekt med den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klassen och Lägg till sökresultat. En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan.  En symbol layer skapas och datakällan har lagts till symbol-lager, som sedan läggs till i kartan.

Senaste kodblocket justerar kamera gränser för kartan med hjälp av kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) egenskapen.

Sökbegäran, datakällan, och symbolen lager och kamera gränser skapas och ställa in i kartan [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) att säkerställa att resultaten visas när kartan har lästs in helt.


## <a name="make-a-search-request-via-xmlhttprequest"></a>Gör en sökbegäran via XMLHttpRequest

<iframe height='500' scrolling='no' title='Visa sökresultat på en karta' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>visa sökresultat på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skickar en [XMLHttpRequest](https://xhr.spec.whatwg.org/) till [Azure Maps Fuzzy-sökning API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) att söka efter orienteringspunkt. Fuzzy-sökning API: et kan hantera valfri kombination av fuzzy indata. 

Det tredje kodblocket Parsar search-svar och lagrar resultatet i en matris att beräkna det giltiga intervallet. Den returnerar sedan sökresultaten.

Det fjärde kodblocket skapar en datakälla objekt med den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klassen och Lägg till sökresultat. En [symbol layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att rendera platsbaserad data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. En symbol layer skapas och datakällan har lagts till symbol-lager, som sedan läggs till i kartan.

Det sista blocket kod skapar en [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) objekt med ett antal resultat och justerar kamera gränser för kartan med hjälp av kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Det renderas sedan resultatet PIN-koder.

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
> [Visa riktningar från A till B](./map-route.md)
