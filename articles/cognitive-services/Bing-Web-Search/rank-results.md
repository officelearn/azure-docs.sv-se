---
title: Hur du använder rangordning för att visa sökresultat - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder rangordning för att visa sökresultat från Bing Web Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: c2e6a553d29f71b0b7ca8d267e8d396ddb2a2922
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875186"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Hur du använder rangordning för att visa resultat för webbsökning i Bing  

Varje sökning svaret innehåller en [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) svar som anger hur du måste visa sökresultatet. Rangordning svaret grupperar resultaten av mainline innehållet och sidopanelen innehåll för en traditionell sökresultat sidan. Om du inte visa resultatet i en traditionell likriktade och sidopanelen format, måste du ange likriktade innehåll högre synlighet än sidopanelen innehållet.  

Inom varje grupp (mainline eller sidopanelen), [objekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matris identifierar den ordning som innehållet måste finnas i. Varje objekt innehåller följande två sätt att identifiera resultatet i ett svar.  

-   `answerType` och `resultIndex` – den `answerType` fältet identifierar svar (till exempel webbsidan eller nyheter) och `resultIndex` identifierar ett resultat i svaret (till exempel nyhetsartikel). Indexet är nollbaserat.  

-   `value` – Det `value` fält innehåller ett ID som matchar ID: T för ett svar eller ett resultat i svaret. Svaret eller resultatet innehålla ID men inte båda.  

Med hjälp av ID är enklare att använda eftersom du bara behöver matcha rangordning ID med ID: T för ett svar eller ett av resultaten. Om ett svar-objekt innehåller en `id` fältet, visa resultat som svaret finns tillsammans. Till exempel om den `News` objektet innehåller de `id` fältet, visa alla nyhetsartiklar tillsammans. Om den `News` objekt omfattar inte den `id` fältet och sedan varje nyhetsartikel innehåller en `id` fältet och svaret rangordning blandas i nyhetsartiklar med resultaten från andra svar.  

Med hjälp av den `answerType` och `resultIndex` är lite mer komplicerad. Du använder `answerType` att identifiera det svar som innehåller resultatet ska visas. Sedan använder du `resultIndex` till index via svarets resultat att få resultat att visa. (Den `answerType` värdet är namnet på fältet i den [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objekt.) Om du ska visa resultat som svaret finns tillsammans rangordning svaret objekt inte innehåller den `resultIndex` fält.  

## <a name="ranking-response-example"></a>Rangordning svar-exempel

Följande visar ett exempel [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). Web-svaret inte innehåller en `id` fält, visa alla webbsidor individuellt baserat på rangordning (varje webbsidan innehåller en `id` fältet). Och eftersom de bilder, videoklipp och relaterade sökningar svar omfattar den `id` fält, visa resultatet av var och en av dessa tillsammans svar baserat på rangordning.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    },  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Baserat på den här rangordning svar, visar det likriktade följande sökresultat:  

-   Det första resultatet för webbsida
-   Alla avbildningar  
-   Andra och tredje webbsidan resultaten  
-   Alla videor  
-   4, 5 och 6 webbsidan resultaten  

Och sidopanelen skulle visa följande sökresultat:  

-   Alla relaterade sökningar  


## <a name="next-steps"></a>Nästa steg

Information om hur du lyfter unranked resultat finns i [befordrar svar som inte rangordnas](./filter-answers.md#promoting-answers-that-are-not-ranked).

Information om hur du begränsar antalet rankad svar i svaret finns i [begränsningen av antalet svar i svaret](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

En C# exempel som använder rangordning för att visa resultat finns i [rangordning självstudie i C#](./csharp-ranking-tutorial.md).
