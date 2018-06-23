---
title: Nyheter Sök-API-Snabbstart | Microsoft Docs
description: Visar hur du kommer igång med Bing News Sök-API.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 9CF6EAF3-42D8-4321-983C-4AC3896E8E03
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2aa1b3de07bd61eebfc1d410cda76c32fc7c958e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354867"
---
# <a name="your-first-news-search-query"></a>Sökfrågan första Nyheter

Du måste hämta en nyckel för prenumerationen kognitiva Services innan du kan göra din första anropet. Om du vill få en nyckel finns [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api).

För att få nyheter endast sökresultat, skulle du skickar en GET-begäran till följande slutpunkt:

```http
https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla begäranden som kommer från en server. Distribuera nyckeln som en del av ett klientprogram ger större möjlighet för en skadlig tredje part att komma åt den. Dessutom ger samtal från en server en enkel uppgradering för framtida versioner av API: et.

Begäran måste ange den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query) frågeparameter som innehåller användarens sökord. Men det är valfritt, begäran måste också ange den [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#mkt) frågeparameter som identifierar marknaden där du vill att resultaten från. En lista över valfria fråga parametrar som `freshness` och `textDecorations`, se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Alla parametervärden för frågan måste vara URL-kodade.

Begäran måste ange den [Ocp-Apim-prenumeration-nyckeln](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#subscriptionkey) huvud. Även om det är valfritt, uppmanas du att ange följande huvuden:

- [Användaragent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientid)
- [X-Sök-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientip)
- [X sökplats](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#location)

Klientens IP- och plats huvuden är viktiga för att returnera var medveten om innehållet.

En lista över alla begärande- och svarshuvuden, finns i [huvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headers).

## <a name="the-request"></a>Begäran

Nedan visas en begäran om nyheter som innehåller alla föreslagna frågeparametrar och rubriker. Om det är första gången du anropar någon av API: er för Bing inte med klient-ID-huvudet. Ta bara med klient-ID om du har tidigare påbörjat en Bing-API och Bing returnerade ett klient-ID för användare och enhet kombination.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="the-response"></a>Svaret

Nedan visas svaret på den tidigare begäranden. Exemplet visar också Bing-specifika svarshuvuden.

```http
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "Sailing College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },
    {
        "name" : "Reunion at Fabrikam Lakes Sailing Club, celebrates 50 years...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.38210...",
                "width" : 650,
                "height" : 366
            }
        },
        "description" : "The reunion on April 29, at Fabrikam Lakes Sailing...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T13:08:00"
    },
    {
        "name" : "Sailing Club to host Dinghy Sailing World...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.364AB41...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    },
    {
        "name" : "A 24-Carat Dinghy",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.6CC2...",
                "width" : 700,
                "height" : 466
            }
        },
        "description" : "“Hard dinghies are for purists, the kind of people who...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-03T12:14:00",
        "category" : "Politics"
    }]
}
```

## <a name="next-steps"></a>Nästa steg

Prova att använda API: et. Gå till [nyheter Sök API Testing-konsolen](https://dev.cognitive.microsoft.com/docs/services/56b43f72cf5ff8098cef380a/operations/56f02400dbe2d91900c68553).

Mer information om förbrukar svar-objekt finns [vad är Bing News Search?](./search-the-web.md). Du hittar också mer information om följande vanliga åtgärder:

- [Hämta dagens de senaste](./search-the-web.md#getting-todays-top-news)
- [Hämta nyheter efter kategori](./search-the-web.md#getting-news-by-category)
- [Hämta trender Nyheter](./search-the-web.md#getting-trending-news)
