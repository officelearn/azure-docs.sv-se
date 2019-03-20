---
title: Sök efter nyheter med API för nyhetssökning i Bing
titlesuffix: Azure Cognitive Services
description: Lär dig hur du skickar sökfrågor för allmänna nyheter, populära ämnen och rubriker.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/11/2019
ms.author: scottwhi
ms.openlocfilehash: 612a3961d901f53147ab2f3cfeea20f9c11d96b7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087864"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Sök efter nyheter med API för nyhetssökning i Bing

Med API för bildsökning i Bing är det enkelt att integrera Bings funktioner för kognitiv nyhetssökning i dina program.

API:et för nyhetssökning i Bing hittar och returnerar i första hand relevanta nyhetsartiklar, men har även flera funktioner för intelligent och fokuserad nyhetssökning på webben.

## <a name="suggest-and-use-search-terms"></a>Föreslå och använda söktermer

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren har angett sin sökterm ska du koda den i en webbadress innan du ställer in frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query). Om användaren till exempel anger *segeljollar* ställer du in `q` till `sailing+dinghies` eller `sailing%20dinghies`.

## <a name="get-general-news"></a>Få allmänna nyheter

För att hämta allmänna nyhetsartiklar relaterade till användarens sökterm från webben skickar du följande GET-förfrågan:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen.

Om du vill hämta nyheter från en specifik domän använder du frågeoperatorn [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Nedan visas svaret på den tidigare frågan. Som en del av [användnings- och visningskraven](../useanddisplayrequirements.md) för API:er för Bing-sökresultat måste du visa nyhetsartiklarna i den ordning som anges i svaret. Om artikeln innehåller klustrade artiklar ska du indikera att den relaterade artikeln finns och visa den om användaren begär det.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
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
    }]
}
```

I svaret med [nyheter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#news) visas de nyhetsartiklar som Bing anser vara relevanta för frågan. Fältet `totalEstimatedMatches` innehåller en uppskattning av antalet artiklar som är tillgängliga för visning. Läs mer om bläddring mellan artiklar i [Sidindela nyheter](../paging-news.md).

Varje [nyhetsartikel](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v5-reference#newsarticle) i listan innehåller artikels namn, beskrivning och webbadressen till artikeln på värdens webbplats. Om artikeln innehåller en bild innehåller objektet även en miniatyr för bilden. Använd `name` och `url` till att skapa en hyperlänk som tar användaren till nyhetsartikeln på värdens webbplats. Om artikeln innehåller en bild ska du även göra den klickbar med `url`. Se till att du tillskriver artikeln med `provider`.

Om Bing kan fastställa vilken kategori nyhetsartikeln tillhör innehåller den även fältet `category`.

## <a name="get-todays-top-news"></a>Hämta dagens viktigaste nyheter

Om du vill hämta dagens viktigaste nyhetsartiklar gör du på samma sätt som när du frågar efter allmänna nyheter förutom att du inte ställer in parametern `q`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Svaret med de viktigaste nyheterna är nästan samma som för allmänna nyheter. `news`-svaret innehåller dock inte fältet `totalEstimatedMatches` eftersom det gäller ett visst antal resultat. Antalet viktiga nyhetsartiklar kan variera beroende på nyhetscykeln. Se till att du tillskriver artikeln med `provider`.

## <a name="get-news-by-category"></a>Hämta nyheter efter kategori

Om du vill hämta nyheter i en viss kategori, som de populäraste artiklarna om sport eller underhållning, skickar du följande GET-förfrågan till Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Använd frågeparametern [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) till att ange vilken typ av artiklar som ska hämtas. En lista med de nyhetskategorier du kan ange finns i [Nyhetskategorier per marknad](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-categories-by-market).

Svaret när du hämtar nyheter i en viss kategori är nästan samma som för allmänna nyheter. Alla artiklar kommer dock från den angivna kategorin.

## <a name="get-headline-news"></a>Hämta toppnyheter

Om du vill hämta de nyhetsartiklar som är placerade längst upp på nyhetssidan oavsett kategori skickar du följande GET-förfrågan till Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Ta inte med frågeparametern [category](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category).

Svaret med toppnyheter är nästan samma som för dagens populäraste nyheter. Om artikeln är en toppnyhet har fältet `headline` värdet **true**.

Svaret innehåller som standard upp till 12 toppnyheter. Om du vill ändra antalet toppnyheter som ska returneras ställer du in frågeparametern [headlineCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headlinecount). Svaret innehåller också upp till fyra nyhetsartiklar per kategori som inte är toppnyheter.

I svaret räknas klustret som en artikel. Eftersom ett kluster kan innehålla flera artiklar så kan svaret innehålla fler än 12 toppnyheter och fler än fyra andra nyhetsartiklar per kategori.

## <a name="get-trending-news"></a>Hämta populära nyheter

Om du vill hämta nyheter som är populära på sociala medier skickar du följande GET-förfrågan till Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> Populära ämnen är bara tillgängliga på marknaderna en-US och zh-CN.

Följande JSON är svaret på föregående förfrågan. Varje populär nyhetsartikel innehåller en relaterad bild, flaggan ”senaste nytt” och en webbadress till Bing-sökresultatet för artikeln. Använd webbadressen i fältet `webSearchUrl` om du vill dirigera användaren till sidan med Bing-sökresultatet. Du kan också använda frågetexten till att anropa [API för webbsökning](../../bing-web-search/search-the-web.md) och visa resultaten själv istället.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Hämta relaterade nyheter

Om det finns andra artiklar som är relaterade till en nyhetsartikel så kan nyhetsartikeln innehålla fältet [clusteredArticles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle-clusteredarticles). Här visas en artikel med klustrade artiklar.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bläddra igenom resultaten för nyhetssökning i Bing](../paging-news.md)
