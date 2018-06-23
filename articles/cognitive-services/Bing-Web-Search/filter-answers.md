---
title: Filtrering web-svar som Bing returnerar | Microsoft Docs
description: Visar hur du använder responseFilter för att filtrera de svar som returnerar Bing webb-API för sökning.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: a5ee6241630ee24a05c2c4b932453bd7946a7508
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352899"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrering av svar som Sök-svaret innehåller  

När du frågar webben returnerar Bing allt innehåll som den anser är relevanta för sökningen. Om frågan ”färdas + dinghies”, kan svaret innehåller följande svar:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

Om du är intresserad av specifika typer av innehåll, till exempel bilder, videor och nyheter, kan du begära endast dessa svar med hjälp av den [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter) Frågeparametern. Om Bing hittar relevant innehåll för de angivna svar, returneras den Bing. Svaret filtret är en kommaavgränsad lista med svar. Följande visar hur du använder `responseFilter` begäran bilder, videor och nyheter avseglingen dinghies. När du kodar frågesträngen ändra kommatecken till %2 C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Nedan visas svaret på den föregående frågan. Som du kan se Bing gick inte att hitta relevanta video och nyheter resultat, så svaret inte inkluderas.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Även om Bing inte returnerade resultat av video och nyheter i föregående svar, innebär det inte att video och nyheter innehåll inte finns. Det innebär bara att sidan inte inkluderas. Men om du [sidan](./paging-webpages.md) via fler resultat de efterföljande sidorna skulle förmodligen inkluderas. Även om du anropar den [Video Sök API](../bing-video-search/search-the-web.md) och [nyheter Sök-API](../bing-news-search/search-the-web.md) slutpunkter direkt, svaret skulle förmodligen innehålla resultat. 

Du är avråder från att använda `responseFilter` att hämta resultat från ett enda API. Om du vill att innehåll från en enda Bing-API anropa denna API direkt. Till exempel för att ta emot endast bilder, skicka en begäran till avbildningen Sök API-slutpunkt `https://api.cognitive.microsoft.com/bing/v7.0/images/search` eller en av de andra [bilder](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#endpoints) slutpunkter. Anropar den enda API är viktiga inte bara av prestandaskäl men eftersom de innehåll-specifika API: er erbjuder bättre resultat. Du kan till exempel använda filter som inte är tillgängliga för webb-API för sökning att filtrera resultatet.  
  
För att få sökresultat från en specifik domän kan inkludera den `site:` frågeoperatorn i frågesträngen.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE] 
> Beroende på frågan, om du använder den `site:` frågeoperatorn, det finns risk att svaret kan innehålla innehåll för vuxna oberoende av den [säker sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#safesearch) inställningen. Du bör använda `site:` endast om du är medveten om innehållet på webbplatsen och ditt scenario stöder möjligheten att vuxet innehåll. 
  
## <a name="limiting-the-number-of-answers-in-the-response"></a>Begränsning av antalet svar i svaret

Bing innehåller svar i svaret, utifrån rangordning. Till exempel om du frågar *färdas + dinghies*, Bing returnerar `webpages`, `images`, `videos`, och `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Begränsar antalet svar som Bing returnerar de två översta svar (webbsidor och bilder), den [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) Frågeparametern till 2. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Svaret innehåller endast `webPages` och `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Om du lägger till den `responseFilter` Frågeparametern till föregående fråga och ange den till webbsidor och nyheter, svaret innehåller endast webbsidor eftersom nyheter inte rangordnas.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Uppgradera svar som inte rangordnas

Om upp rangordnas svar som Bing returnerar för en fråga webbsidor, bilder, videor och relatedSearches, inkluderar svaret dessa svar. Om du ställer in [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) till två (2), Bing returnerar de översta två ranked svar: webbsidor och bilder. Om du vill Bing att inkludera bilder och videor i svaret, ange den [befordra](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) Frågeparametern och ange den till bilder och -videoklipp. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Följande är svaret på begäran. Bing returnerar de översta två svar, webbsidor och bilder och främjar videor i svaret.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Om du ställer in `promote` till nyheter, svaret inte innehåller nyheter svaret eftersom det inte är ett ranked svar&mdash;du kan flytta enbart rangordnas svar.

Svaren som du vill flytta upp räknas inte mot den `answerCount` gränsen. Om exempelvis ranked svaren är nyheter, bilder och videoklipp och du ställer in `answerCount` till 1 och `promote` till nyheter, svaret innehåller nyheter och bilder. Eller, om ranked svaren videor, bilder och nyheter, svaret innehåller videor och nyheter.

Du kan använda `promote` endast om du anger den `answerCount` Frågeparametern.
