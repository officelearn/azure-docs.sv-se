---
title: Skicka och använda API-frågor och svar - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill lära dig hur du skickar och använder sökfrågor med API:et för lokal företagssökning på Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 25bcdb89002fec4f9b67b091996d7bf80bcf21c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74326727"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Skicka och använda API-frågor och svar för Bing Local Business Search

Du kan få lokala resultat från API:et för sökning i Bing `Ocp-Apim-Subscription-Key` lokalt företag genom att skicka en sökfråga till slutpunkten och inkludera huvudet, vilket krävs. Tillsammans med tillgängliga [rubriker](local-search-reference.md#headers) och [parametrar](local-search-reference.md#query-parameters)kan sökningar anpassas genom att ange [geografiska gränser](specify-geographic-search.md) för det område som ska sökas och [kategorierna](local-search-query-response.md) av platser returneras.

## <a name="creating-a-request"></a>Skapa en begäran

Om du vill skicka en begäran till API:et för `q=` lokal företagssökning i Bing lägger `Ocp-Apim-Subscription-Key` du till en sökterm i parametern innan du lägger till den i API-slutpunkten och inklusive huvudet. Ett exempel:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Syntaxen för url-begäran visas nedan. Mer information om hur du skickar begäranden finns i [snabbstarten](quickstarts/local-quickstart.md)för Bing Local Business Search API och referensinnehåll för [rubriker](local-search-reference.md#headers) och [parametrar.](local-search-reference.md#query-parameters) 

Information om lokala sökkategorier finns i [Sökkategorier för API:et för lokal företagssökning.](local-categories.md)

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Använda svar

JSON-svar från API:et för `SearchResponse` lokal företagssökning i Bing innehåller ett objekt. API:et returnerar relevanta `places` sökresultat i fältet. Om inga resultat hittas inkluderas inte fältet `places` i svaret.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Sökresultatattribut

JSON-resultaten som returneras av API:et innehåller följande attribut:

* _type
* adress
* enhetPresentationInfo
* Geo
* id
* namn
* ruttbarPunkt
* telefon
* url

Allmän information om rubriker, parametrar, marknadskoder, svarsobjekt, fel osv., se [Bing Local Search API v7-referensen.](local-search-reference.md)

> [!NOTE]
> Du, eller en tredje part för din räkning, får inte använda, behålla, lagra, cachelagra, dela eller distribuera data från API:et för lokal sökning i syfte att testa, utveckla, träna, distribuera eller tillgängliggöra tjänster eller funktioner som inte kommer från Microsoft. 


## <a name="example-json-response"></a>Exempel på JSON-svar

Följande JSON-svar innehåller sökresultat som `?q=restaurant+in+Bellevue`anges av frågan .

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Nästa steg
- [Snabbstart för lokal företagssökning](quickstarts/local-quickstart.md)
- [Snabbstart för Java-sökning för lokal företagssökning](quickstarts/local-search-java-quickstart.md)
- [Snabbstart för söknod för lokalt företag](quickstarts/local-search-node-quickstart.md)
- [Snabbstart för Lokal företagssökning i Python](quickstarts/local-search-python-quickstart.md)
