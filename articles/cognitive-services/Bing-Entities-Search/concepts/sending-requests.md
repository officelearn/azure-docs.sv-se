---
title: Skicka sökbegäranden till API:et för entitetssökning i Bing
titleSuffix: Azure cognitive Services
description: Lär dig hur du skickar sökbegäranden till API:et för entitetssökning i Bing
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 8fd0ccff9875086f46ca001660b0ee954d3e539b
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869965"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Skicka sökbegäranden till API:et för entitetssökning i Bing

API:et för entitetsökning i Bing skickar en sökfråga till Bing och hämtar resultat som innehåller entiteter och platser. Platsresultat omfattar restauranger, hotell eller andra lokala företag. För platser kan frågan specificera namnet på det lokala företaget eller fråga efter en lista (exempelvis restauranger i närheten). Entitetsresultat omfattar personer, platser eller saker. Plats i det här sammanhanget är turistattraktioner, tillstånd, länder/regioner, osv. 

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="the-endpoint"></a>Slutpunkten

För att få sökresultat för entitet och plats skickar du en GET-begäran till följande slutpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Begäranden måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Om nyckeln distribueras som en del av ett klientprogram ökar risken för att en illvillig tredje part kan komma åt den. Anrop från en server innebär dessutom att det bara finns en enda uppgraderingspunkt för framtida versioner av API:et.

## <a name="specifying-query-parameters-and-headers"></a>Specificera frågeparametrar och huvuden

Begäran måste innehålla frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query), som innehåller användarens sökterm. Begäran måste även specificera frågeparametern [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt), som identifierar den marknad som du vill att resultatet ska komma från. En lista över valfria frågeparametrar finns i [Frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). URL-koda alla frågeparametrar.  
  
Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey). Även om det är valfritt rekommenderar vi även att följande huvuden finns med:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Sidhuvuden för klientens IP-adress och platsen är viktiga för att returnera platsmedvetet innehåll.  

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Begäran

Nedan visas en entitetsbegäran som innehåller alla föreslagna frågeparametrar och huvuden. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen.

## <a name="the-response"></a>Svaret

Nedan visas svaret på den tidigare begäran. Exemplet visar också Bing-specifika svarshuvuden. Mer information om svarsobjektet finns i [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Nästa steg

* [Söka efter entiteter med API för entiteter i Bing](search-for-entities.md)
* [Användnings- och visningskrav för Bing-API](../use-display-requirements.md)