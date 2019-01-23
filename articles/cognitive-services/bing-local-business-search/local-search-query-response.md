---
title: Skicka och använda lokala företag i Bing med frågor och svar | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill lära dig hur du skickar och använda sökfrågor med den lokala företag i Bing.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh; v-gedod
ms.openlocfilehash: 73aa9756d7a3af4c6f7185533458e0e322b72666
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452385"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Skicka och använda lokala företag i Bing med frågor och svar

Du kan hämta lokala resultat från Bing-API för sökning av lokala företag genom att skicka en sökfråga till dess slutpunkt och inklusive den `Ocp-Apim-Subscription-Key` huvud som krävs. Tillsammans med tillgängliga [rubriker](local-search-reference.md#headers) och [parametrar](local-search-reference.md#query-parameters), sökningar kan anpassas genom att ange [geografiska gränser](specify-geographic-search.md) för området som ska genomsökas och [kategorier](local-search-query-response.md) av platser som returneras.

## <a name="creating-a-request"></a>Skapa en begäran

Om du vill skicka en begäran till den lokala företag i Bing, lägger du till en sökterm till den `q=` parametern innan du lägger till den API-slutpunkten och inklusive den `Ocp-Apim-Subscription-Key` rubrik. Exempel:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Fullständig begärd URL-syntaxen visas nedan. Finns i den lokala företag i Bing [snabbstarter](quickstarts/local-quickstart.md), och referera till innehåll för [rubriker](local-search-reference.md#headers) och [parametrar](local-search-reference.md#query-parameters) för mer information om hur du skickar begäranden. 

Information om lokala Sök-kategorier finns i [Sök kategorier för den lokala företag i Bing](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Med hjälp av svar

JSON-svaren från den lokala företag i Bing innehåller en `SearchResponse` objekt. API: et returnerar relevanta sökresultat i den `places` fält. Om det finns inga resultat i `places` fältet ska inte ingå i svaret.

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

### <a name="search-result-attributes"></a>Attribut för Search-resultat

JSON-resultaten som returnerades av API: et omfattar följande attribut:

* _typ
* Adress
* entityPresentationInfo
* geo
* id
* namn
* routeablePoint
* Telefon
* url

Allmän information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [lokal sökning i Bing v7](local-search-reference.md) referens.

> [!NOTE]
> Du eller en tredje part för din räkning kan inte använda, behålla, lagra, cache, delar, eller distribuera alla data från lokala Sök-API för testning, utveckling, utbildning, distribuerar eller gör tillgänglig någon icke-Microsoft-tjänst eller funktion. 


## <a name="example-json-response"></a>Exempel-JSON-svar

Följande JSON-svaret innehåller sökresultat som anges av frågan `?q=restaurant+in+Bellevue`.

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
- [Lokala företag Search-Snabbstart](quickstarts/local-quickstart.md)
- [Lokala företag Search-Java-Snabbstart](quickstarts/local-search-java-quickstart.md)
- [Lokala företag Search-Node-quickstart](quickstarts/local-search-node-quickstart.md)
- [Lokala företag Search Python-Snabbstart](quickstarts/local-search-python-quickstart.md)