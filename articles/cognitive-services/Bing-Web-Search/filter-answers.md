---
title: Så här filtrerar du sökresultat - API för webbsökning på Bing
titleSuffix: Azure Cognitive Services
description: Du kan filtrera de typer av svar som Bing innehåller i svaret (till exempel bilder, videor och nyheter) med hjälp av frågeparametern "responseFilter".
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220274"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrera svaren som söksvaret innehåller  

När du frågar på webben returnerar Bing allt relevant innehåll som hittas för sökningen. Om sökfrågan till exempel är "sailing+dinghies" kan svaret innehålla följande svar:

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

## <a name="query-parameters"></a>Frågeparametrar

Om du vill filtrera svaren som returneras av Bing använder du frågeparametrarna nedan när du anropar API:et.  

### <a name="responsefilter"></a>Svarsfilter

Du kan filtrera de typer av svar som Bing innehåller i svaret (till exempel bilder, videor och nyheter) med hjälp av frågeparametern [responseFilter,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) som är en kommaavgränsad lista med svar. Ett svar kommer att inkluderas i svaret om Bing hittar relevant innehåll för det. 

Om du vill utesluta specifika svar från svaret, till exempel bilder, förbereder du ett `-` tecken till svarstypen. Ett exempel:

```
&responseFilter=-images,-videos
```

Följande visar hur `responseFilter` du använder för att begära bilder, videor och nyheter om segling jollar. När du kodar frågesträngen ändras kommatecken till %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Nedan visas svaret på den tidigare frågan. Eftersom Bing inte hittade relevanta video- och nyhetsresultat innehåller svaret dem inte.

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

Även om Bing inte returnerar video- och nyhetsresultat i föregående svar betyder det inte att video- och nyhetsinnehåll inte finns. Det betyder helt enkelt att sidan inte innehöll dem. Men om du [bläddrar](./paging-webpages.md) igenom fler resultat, skulle de efterföljande sidorna sannolikt innehålla dem. Om du anropar [API:et för videosökning](../bing-video-search/search-the-web.md) och [API-slutpunkter för nyhetssökning](../bing-news-search/search-the-web.md) direkt, skulle svaret sannolikt innehålla resultat.

Du avråds `responseFilter` från att använda för att få resultat från ett enda API. Om du vill ha innehåll från ett enda Bing API anropar du det API:et direkt. Om du till exempel bara vill ta emot bilder skickar `https://api.cognitive.microsoft.com/bing/v7.0/images/search` du en begäran till API-slutpunkten för bildsökning eller någon av de andra [slutpunkterna Bilder.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) Att anropa det enda API:et är viktigt inte bara av prestandaskäl utan för att de innehållsspecifika API:erna ger rikare resultat. Du kan till exempel använda filter som inte är tillgängliga för webbsök-API:et för att filtrera resultaten.  

### <a name="site"></a>Plats

Om du vill hämta sökresultat från `site:` en viss domän inkluderar du frågeparametern i frågesträngen.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Beroende på frågan, om `site:` du använder frågeoperatorn, finns det en risk att svaret kan innehålla barnförbjudet innehåll oavsett [safeSearch-inställningen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) Du bör endast använda `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn.

### <a name="freshness"></a>Uppdateringsbarhet

Om du vill begränsa webbsvarsresultaten till webbsidor som Bing upptäckte under en viss period anger du frågeparametern [för färskhet](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) till ett av följande skiftlägesokänsliga värden:

* `Day`— Returnera webbsidor som Bing upptäckt under de senaste 24 timmarna
* `Week`— Returnera webbsidor som Bing upptäckt under de senaste 7 dagarna
* `Month`— Returnera webbsidor som upptäckts under de senaste 30 dagarna

Du kan också ange den här parametern `YYYY-MM-DD..YYYY-MM-DD`till ett anpassat datumintervall i formuläret . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Om du vill begränsa resultaten till ett enda datum ställer du in färskhetsparametern till ett visst datum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Resultaten kan omfatta webbsidor som faller utanför den angivna perioden om antalet webbsidor som Bing matchar dina filtervillkor är mindre än antalet webbsidor du har begärt (eller standardnumret som Bing returnerar).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Begränsa antalet svar i svaret

Bing kan returnera flera svarstyper i JSON-svaret. Om du till exempel frågar *segling+jollar* `images`kan `videos`Bing returnera `webpages`, , och `relatedSearches`.

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

Om du vill begränsa antalet svar som Bing returnerar till de två översta svaren (webbsidor och bilder) ställer du in parametern [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) query till 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Svaret innehåller `webPages` endast `images`och .

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

Om du `responseFilter` lägger till frågeparametern i föregående fråga och ställer in den på webbsidor och nyheter innehåller svaret endast webbsidor eftersom nyheter inte rangordnas.

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

## <a name="promoting-answers-that-are-not-ranked"></a>Främja svar som inte rankas

Om de högst rankade svaren som Bing returnerar för en fråga är webbsidor, bilder, videor och relatedSearches, skulle svaret innehålla dessa svar. Om du ställer in [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) till två (2) returnerar Bing de två högst rankade svaren: webbsidor och bilder. Om du vill att Bing ska inkludera bilder och videor i svaret anger du parametern [befordra](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) fråga och ställer in den på bilder och videor.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Följande är svaret på ovanstående begäran. Bing returnerar de två bästa svaren, webbsidorna och bilderna och marknadsför videor till svaret.

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

Om du `promote` ställer in på nyheter innehåller svaret inte nyhetssvaret eftersom&mdash;det inte är ett rankat svar som du bara kan marknadsföra rankade svar.

Svaren som du vill marknadsföra räknas `answerCount` inte mot gränsen. Om de rankade svaren till exempel är nyheter, bilder `answerCount` och videor, och du ställer in på 1 och `promote` nyheter, innehåller svaret nyheter och bilder. Eller, om de rankade svaren är videor, bilder och nyheter, innehåller svaret videor och nyheter.

Du får `promote` bara använda `answerCount` om du anger frågeparametern.
