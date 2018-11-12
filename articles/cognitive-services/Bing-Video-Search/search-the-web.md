---
title: Vad är videosökning i Bing?
titlesuffix: Azure Cognitive Services
description: Visar hur du använder API för videosökning i Bing till att söka efter videor på internet.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: overview
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: e48a0a056628e0c863330de792f8edfaa48aae34
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261867"
---
# <a name="what-is-bing-video-search"></a>Vad är videosökning i Bing?

API för videosökning i Bing fungerar ungefär (men inte exakt) som [Videosökning i Bing](https://www.bing.com/videos). Med API för videosökning i Bing kan du skicka sökfrågor till Bing och få tillbaka en lista med relevanta videor.

Om du utvecklar en sökresultatsida endast för video för att hitta videor som är relevanta för användarens sökfråga anropar du detta API i stället för att anropa det mer generella [API för webbsökning i Bing](../bing-web-search/search-the-web.md). Om du vill hitta videor och andra typer av innehåll som webbplatser, nyheter och bilder anropar du API för webbsökning i Bing.

## <a name="suggesting--using-search-terms"></a>Föreslå och använda söktermer

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren har angett sin sökterm ska du koda den i en webbadress innan du ställer in frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query). Om användaren till exempel anger *segeljollar* ställer du in `q` till `sailing+dinghies` eller `sailing%20dinghies`.

## <a name="getting-videos"></a>Hämta videor

För att hämta videor relaterade till användarens söktermer från webben skickar du följande GET-förfrågan:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Alla begäranden måste ske från en server.

Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen.

Om du vill hämta videor från en specifik domän använder du frågeoperatorn [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Svaret innehåller ett [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos)-svar som innehåller en lista med videor som Bing anser vara relevanta för frågan. Varje [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video)-objekt i listan innehåller videons webbadress, längd, storlek, kodningsformat och andra attribut. Videoobjektet innehåller även webbadressen till en miniatyrbild av videon och miniatyrbildens mått.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

Du kan visa ett collage med alla miniatyrbilder eller bara vissa av miniatyrbilderna. Om du visar en delmängd bör du ge användaren möjlighet att visa återstående videor. Du måste visa videoklippen i den ordning de anges i svaret. Information om att ändra storlek på miniatyrbilden finns i [Resizing and Cropping Thumbnails](./resize-and-crop-thumbnails.md) (Ändra storlek på och beskär miniatyrbilder). 

När användaren för muspekaren över miniatyrbilden kan du använda [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) till att spela upp en miniatyrversion av videon. Se till att du tillskriver ursprunget när du visar en video.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Om användaren klickar på miniatyrbilden finns följande visningsalternativ för videon:

- Använd [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) till att visa videon på ursprungswebbplatsen (till exempel YouTube)
- Använd [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) till att visa videon i Bing-spelaren
- Använd [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) till att bädda in videon i en egen upplevelse 

Glöm inte att ange videons utgivare och upphovsman när du spelar upp den.

Mer information om hur du använder [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) till att få insikter om videon finns i [Videoinsikter](./video-insights.md).

## <a name="filtering-videos"></a>Filtrera videor

Som standard returnerar API för videosökning i Bing alla videor som är relevanta för frågan. Om du bara vill visa kostnadsfria videor eller klipp som är kortare än fem minuter skulle du använda följande filterparametrar i frågan:

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing)&mdash;Filtrera videor efter pris (till exempel videor som är kostnadsfria att se eller sådana du behöver betala för)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution)&mdash;Filtrera videor efter upplösning (till exempel videor med upplösningen 720p eller högre)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength)&mdash;Filtrera videor efter deras längd (till exempel videor som är kortare än fem minuter)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness)&mdash;Filtrera videor efter ålder (till exempel videor som upptäckts av Bing den senaste veckan)

Om du vill hämta videor från en specifik domän tar du med frågeoperatorn [site:](https://msdn.microsoft.com/library/ff795613.aspx) i frågesträngen.

> [!NOTE]
> Beroende på frågan finns det en risk när du använder frågeoperatorn `site:` att svaret har innehåll som är olämpligt för barn, oberoende av inställningen för [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch). Du bör endast använda `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn.

I följande exempel visas hur du hämtar kostnadsfria videor från ContosoSailing.com med upplösningen 720p eller högre och som Bing har identifierat den senaste månaden.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Expandera frågan

Om Bing kan expandera frågan för att begränsa den ursprungliga sökningen innehåller objektet [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) fältet `queryExpansions`. Om frågan till exempel var *Cleaning Gutters* (rensa stuprännor) kan de expanderade frågorna vara Cleaning Gutters **Tools** (verktyg), Cleaning Gutters **From the Ground** (från marken), Gutter Gleaning  **Machine** (maskin) och **Easy** Gutter Cleaning.

Här ser du de expanderade frågorna för *Cleaning Gutters*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

Fältet `queryExpansions` innehåller en lista över [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj)-objekt. Fältet `text` innehåller den expanderade frågan, och fältet `displayText` innehåller expansionstermen. Du kan använda text- och miniatyrfälten till att visa de expanderade frågesträngarna för användaren om en expanderad frågesträng är det användaren egentligen letar efter. Gör miniatyrbilden och texten klickbara genom att använda URL:en `webSearchUrl` eller URL:en `searchLink`. Använd `webSearchUrl` för att skicka användaren till Bing-sökresultat eller `searchLink` om du tillhandahåller din egen resultatsida.

## <a name="pivoting-the-query"></a>Pivotera frågan

Om Bing kan segmentera den ursprungliga sökfrågan innehåller objektet [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) fältet `pivotSuggestions`. Om den ursprungliga frågan till exempel var *Cleaning Gutters* kan Bing segmentera frågan till *Cleaning* och *Gutters*.

Här är ett exempel på pivotförslagen för *Cleaning Gutters*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

För varje pivot innehåller svaret en lista över [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj)-objekt som innehåller föreslagna frågor. Fältet `text` innehåller den föreslagna frågan, och fältet `displayText` innehåller den term som ersätter pivoten i den ursprungliga frågan. Till exempel Window Cleaning (fönsterputsning).

Du kan använda fälten `text` och `thumbnail` för att visa användaren de expanderade frågesträngarna om den expanderade frågesträngen är det användaren letar efter. Gör miniatyrbilden och texten klickbara genom att använda URL:en `webSearchUrl` eller URL:en `searchLink`. Använd `webSearchUrl` för att skicka användaren till Bing-sökresultat eller `searchLink` om du tillhandahåller din egen resultatsida.

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Kom igång snabbt med din första begäran genom att läsa [Göra din första begäran](./quick-start.md).

Om du behöver hjälp med att hitta din prenumerationsnyckel kan du läsa i [Prenumerationsnycklar](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api).

Bekanta dig med referensen till [API för videosökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference). Referensen innehåller listan över slutpunkter, rubriker och frågeparametrar som du använder för att begära sökresultat. Den omfattar även definitioner av svarsobjekten. 

För att förbättra användarfunktionen för sökrutan läser du [API för automatiska förslag i Bing](../bing-autosuggest/get-suggested-search-terms.md). När användaren anger sina frågetermer kan du anropa det här API:t för att få relevanta frågetermer som har använts av andra användare.

Se till att läsa [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna om användning av sökresultat.

När du anropar API för videosökning i Bing returneras en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. Fältet `totalEstimatedMatches` i svaret innehåller en uppskattning av antalet videor som finns att visa. Mer information om hur du bläddrar bland återstående videor finns i [Sidindela videor](./paging-videos.md).

Mer information om hur du får insikter om en video finns i [Videoinsikter](./video-insights.md).

Mer information om hur du hämtar populära videor finns i [Trendande video](./trending-videos.md).