---
title: Snabbstart för entiteter Sök API | Microsoft Docs
description: Visar hur du kommer igång med Bing entiteter Sök-API.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: B206A254-B7E9-49FF-AFD5-87B1E4D6D30B
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 12031d2447920c7e2d6180f35cf4fb29aa1b6150
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351525"
---
# <a name="making-your-first-entities-request"></a>Gör din första entiteter för begäran

Sök-API för entiteten skickar en fråga till Bing och hämtar resultat som innehåller entiteter och platser. Det inkluderar plats resultaten hotell, hotell eller andra lokala företag. Frågan kan ange namnet på det lokala företaget för platser, eller be en lista (till exempel hotell närheten). Entiteten resultat innehåller personer, platser eller saker. I den här kontexten används turistattraktioner, tillstånd, länder och så vidare. 

## <a name="first-steps"></a>Första stegen

Du måste hämta en nyckel för prenumerationen kognitiva Services innan du kan göra din första anropet. Om du vill få en nyckel finns [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Om Sök-API för enheter som inte visas överst klickar du på den **Sök** fliken och rulla nedåt tills du ser det.)

## <a name="the-endpoint"></a>Slutpunkten

För att hämta entiteten och placera sökresultat, skicka en GET-begäran till följande slutpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla begäranden som kommer från en server. Distribuera nyckeln som en del av ett klientprogram ger större möjlighet för en skadlig tredje part att komma åt den. Dessutom ger samtal från en server en enkel uppgradering för framtida versioner av API: et.

## <a name="specifying-query-parameters-and-headers"></a>Ange frågeparametrar och rubriker

Begäran måste ange den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) frågeparameter som innehåller användarens sökord. Begäran måste även ange den [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt) frågeparameter som identifierar marknaden där du vill att resultaten från. En lista med valfria parametrar finns [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). URL: en koda alla Frågeparametrar.  
  
Begäran måste ange den [Ocp-Apim-prenumeration-nyckeln](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey) huvud. Även om det är valfritt, uppmanas du att ange följande huvuden:  
  
-   [Användaragent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X sökplats](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Klientens IP- och plats huvuden är viktiga för att returnera var medveten om innehållet.  

En lista över alla begärande- och svarshuvuden, finns i [huvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Begäran

Nedan visas en begäran om enheter som innehåller alla föreslagna frågeparametrar och rubriker. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Om det är första gången du anropar någon av API: er för Bing inte med klient-ID-huvudet. Ta bara med klient-ID om du har tidigare påbörjat en Bing-API och Bing returnerade ett klient-ID för användare och enhet kombination.

## <a name="the-response"></a>Svaret

Nedan visas svaret på den tidigare begäranden. Exemplet visar också Bing-specifika svarshuvuden. Information om objektet response finns [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```
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

Prova att använda API: et. Gå till [entiteter söka API testning konsolen](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/). 

Mer information om förbrukar svar-objekt finns [söka på webben för entiteter och platser](./search-the-web.md).

Se till att läsa [Bing användas och visa krav](./use-display-requirements.md) så att du inte delar någon av reglerna om hur du använder sökresultatet.
