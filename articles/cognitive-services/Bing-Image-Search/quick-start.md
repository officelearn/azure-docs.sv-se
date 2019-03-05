---
title: 'Snabbstart för API: et Search-avbildningar | Microsoft Docs'
description: Visar hur du kommer igång med hjälp av Sök-API för Bing-avbildningar.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 18d5e17c4d44d396ca0044fbaf023cb4242555b5
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341297"
---
# <a name="your-first-images-search-query"></a>Dina avbildningar från första sökfråga

Innan du kan göra ditt första anrop måste du skaffa en prenumerationsnyckel för Cognitive Services för Bing-sökning. Information om hur du skaffar en nyckel finns i [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api).  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

För att få bildsökningsresultat, skulle du skickar en GET-begäran till slutpunkten för följande:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Om nyckeln distribueras som en del av ett klientprogram ökar risken för att en illvillig tredje part kan komma åt den. Anrop från en server innebär dessutom att det bara finns en enda uppgraderingspunkt för framtida versioner av API:et.

Begäran måste innehålla frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query), som innehåller användarens sökterm. Även om det är valfritt bör begäran även innehålla frågeparametern [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#mkt), som identifierar marknaden som du vill att resultatet ska komma från. En lista över valfria frågeparametrar som `freshness` och `size` finns i [Frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Alla frågeparametervärden måste vara URL-kodade.  
  
Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#subscriptionkey). Även om det är valfritt rekommenderar vi även att följande huvuden finns med:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#location)  

Sidhuvuden för klientens IP-adress och platsen är viktiga för att returnera platsmedvetet innehåll.  

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers).

## <a name="the-request"></a>Begäran

Nedan visas en sökbegäran som innehåller alla föreslagna frågeparametrar och sidhuvuden. Om det är först gången anropar någon av Bing-API: er, omfattar inte rubrik för klient-ID. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Nedan visas svaret på den tidigare begäran.

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

Testa API:et. Gå till [bild-API för webbsökning Testing-konsolen](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

Mer information om hur du använder svarsobjekten finns i [Söka efter videor på webben](./search-the-web.md).

Mer information om hur du får insikter om en bild, till exempel webbsidor som innehåller den eller de personer som har identifierats i avbildningen finns i [information om bilder](./image-insights.md).  
  
Mer information om avbildningar som är populära i sociala medier finns i [populära bilder](./trending-images.md).  
