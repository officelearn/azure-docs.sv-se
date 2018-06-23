---
title: Bilder Sök API Snabbstart | Microsoft Docs
description: Visar hur du kommer igång med Bing bilder Sök-API.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 9e211cf5acd17ab80948d0b7161bdd2a9220c4a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351621"
---
# <a name="your-first-images-search-query"></a>Bilderna första söka fråga

Du måste hämta en nyckel för Bing Search-kognitiva tjänster prenumeration innan du kan göra din första anropet. Om du vill få en nyckel finns [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api).

För att hämta bilden sökresultat, skulle du skickar en GET-begäran till följande slutpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla begäranden som kommer från en server. Distribuera nyckeln som en del av ett klientprogram ger mer möjlighet för skadliga tredjeparts att komma åt den. Dessutom ger samtal från en server en enkel uppgradering för framtida versioner av API: et.

Begäran måste ange den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) frågeparameter som innehåller användarens sökord. Men det är valfritt, begäran måste också ange den [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#mkt) frågeparameter som identifierar marknaden där du vill att resultaten från. En lista över valfria fråga parametrar som `freshness` och `size`, se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Alla parametervärden för frågan måste vara URL-kodade.  
  
Begäran måste ange den [Ocp-Apim-prenumeration-nyckeln](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#subscriptionkey) huvud. Även om det är valfritt, uppmanas du att ange följande huvuden:  
  
-   [Användaragent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientid)  
-   [X-Sök-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientip)  
-   [X sökplats](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#location)  

Klientens IP- och plats huvuden är viktiga för att returnera var medveten om innehållet.  

En lista över alla begärande- och svarshuvuden, finns i [huvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers).

## <a name="the-request"></a>Begäran

Nedan visas en sökbegäran som innehåller alla föreslagna frågeparametrar och rubriker. Om det är först gången anropar någon av Bing-API: er, inte med klient-ID-huvudet. Ta bara med klient-ID om du har tidigare påbörjat en Bing-API och Bing returnerade ett klient-ID för användare och enhet kombination. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Nedan visas svaret på den tidigare begäranden.

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "totalEstimatedMatches" : 838,
    "value" : [
        {
            "name" : "File:Rich Passage Minto Sailing Dinghy.jpg - Wikipedia",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
            "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
            "datePublished" : "2011-10-29T11:26:00",
            "contentUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
            "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
            "contentSize" : "79239 B",
            "encodingFormat" : "jpeg",
            "hostPageDisplayUrl" : "en.wikipedia.org\/wiki\/File:Rich_Passage...",
            "width" : 526,
            "height" : 688,
            "thumbnail" : {
                "width" : 229,
                "height" : 300
            },
            "imageInsightsToken" : "ccid_GNarK7ma*mid_CCF85447ADA6...",
            "insightsSourcesSummary" : {
                "shoppingSourcesCount" : 0,
                "recipeSourcesCount" : 0
            },
            "imageId" : "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
            "accentColor" : "376094"
        },
        . . .
    ],
    "queryExpansions" : [
        {
            "text" : "Small Sailing Dinghies",
            "displayText" : "Small",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4...",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
            }
        },
        . . .
    ],
    "nextOffset" : 10,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Tender",
                    "displayText" : "Tender",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
                    }
                },
                . . .
            ]
        }
    ],
    "displayShoppingSourcesBadges" : false,
    "displayRecipeSourcesBadges" : true
}
```

## <a name="next-steps"></a>Nästa steg

Prova att använda API: et. Gå till [bild Sök API Testing-konsolen](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

Mer information om förbrukar svar-objekt finns [söka på webben](./search-the-web.md).

Mer information om att få insikter om en avbildning, till exempel webbsidor som innehåller den eller de personer som har identifierats i bilden finns [avbildningen insikter](./image-insights.md).  
  
Mer information om avbildningar som trender på sociala medier finns [trender bilder](./trending-images.md).  
