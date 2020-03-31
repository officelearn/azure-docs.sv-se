---
title: Visa sökresultat på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du utför en sökbegäran med Microsoft Azure Maps Web SDK och visar resultaten på kartan.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371400"
---
# <a name="show-search-results-on-the-map"></a>Visa sökresultat på kartan

I den här artikeln visas hur du söker efter plats av intresse och visar sökresultaten på kartan.

Det finns två sätt att söka efter en plats av intresse. Ett sätt är att använda en tjänstmodul för att göra en sökbegäran. Det andra sättet är att göra en sökbegäran till [Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) via Fetch [API](https://fetch.spec.whatwg.org/). Båda sätten diskuteras nedan.

## <a name="make-a-search-request-via-service-module"></a>Gör en sökbegäran via servicemodul

<iframe height='500' scrolling='no' title='Visa sökresultat på en karta (Servicemodul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se penen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Visa sökresultat på en karta (ServiceModul)</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första blocket ett kartobjekt och anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra kodblocket `TokenCredential` skapar en för att autentisera HTTP-begäranden till Azure Maps med åtkomsttoken. Den skickar `TokenCredential` sedan `atlas.service.MapsURL.newPipeline()` till och skapar en [Pipeline-instans.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Representerar `searchURL` en URL till Azure Maps [Search-åtgärder.](https://docs.microsoft.com/rest/api/maps/search)

Det tredje kodblocket skapar ett datakällobjekt med hjälp av klassen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) och lägger till sökresultat i det. Ett [symbollager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att återge punktbaserade data som är indelat i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan.  Ett symbollager skapas sedan. Datakällan läggs till i symbollagret, som sedan läggs till på kartan.

Det fjärde kodblocket använder [Metoden SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) i [tjänstmodulen](how-to-use-services-module.md). Det låter dig utföra en fri form textsökning via [Get Search Fuzzy vila API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) för att söka efter intressepunkt. Hämta begäranden till Api:et för sökfuss kan hantera valfri kombination av suddiga indata. En GeoJSON-funktionssamling från svaret extraheras sedan med `geojson.getFeatures()` hjälp av metoden och läggs till datakällan, vilket automatiskt resulterar i att data återges på kartan via symbollagret.

Det sista kodblocket justerar kameragränserna för kartan med hjälp av kartans [setCamera-egenskap.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)

Sökbegäran, datakällan, symbollagret och kamera bounds finns inuti [kartans händelseavlyssnare.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) Vi vill se till att resultaten visas efter att kartan har laddats helt.


## <a name="make-a-search-request-via-fetch-api"></a>Gör en sökbegäran via Hämta API

<iframe height='500' scrolling='no' title='Visa sökresultat på en karta' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se penen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Visa sökresultat på en</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>karta av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan konstruerar det första kodblocket ett kartobjekt. Den anger autentiseringsmekanismen för att använda åtkomsttoken. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra kodblocket skapar en URL som du vill göra en sökbegäran till. Det skapar också två matriser för att lagra gränser och stift för sökresultat.

Det tredje kodblocket använder [Hämta API](https://fetch.spec.whatwg.org/). [Hämta API](https://fetch.spec.whatwg.org/) används för att göra en begäran till Azure [Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) för att söka efter intressanta platser. Fuzzy search API kan hantera valfri kombination av suddiga indata. Den hanterar och tolkar sedan söksvaret och lägger till resultatstiften i searchPins-matrisen.

Det fjärde kodblocket skapar ett datakällobjekt med hjälp av klassen [DataSource.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) I koden lägger vi till sökresultat i källobjektet. Ett [symbollager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att återge punktbaserade data som är indelat i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. Ett symbollager skapas sedan. Datakällan läggs till i symbollagret, som sedan läggs till på kartan.

Det sista kodblocket skapar ett [BoundingBox-objekt.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) Den använder matrisen av resultat, och sedan justerar kameran gränser för kartan med hjälp av kartans [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Det återger sedan resultatstiften.

Sökbegäran, datakällan, symbollagret och kamera bounds ställs in i kartans [händelseavlyssnare](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att säkerställa att resultaten visas när kartan har laddats helt.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metodtips för att använda söktjänsten](how-to-use-best-practices-for-search.md)

Läs mer om **Fuzzy Search:**

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Se följande artiklar för fullständiga kodexempel:

> [!div class="nextstepaction"]
> [Hämta information från en koordinat](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Visa anvisningar från A till B](map-route.md)
