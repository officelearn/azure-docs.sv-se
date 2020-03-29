---
title: Så här använder du rankningar för att visa sökresultat - API för webbsökning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder rankning för att visa sökresultat från API:et för webbsökning på Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 677f6089f649aae720a6303a7e1512e3c7ebeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "66390123"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Så här använder du rankning för att visa API-resultat för webbsökning på Bing  

Varje söksvar innehåller ett [RankResponse-svar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) som anger hur du måste visa sökresultaten. Rankningssvaret grupperar resultat efter huvudlinjeinnehåll och sidofältsinnehåll för en traditionell sökresultatsida. Om du inte visar resultaten i ett traditionellt huvudlinje- och sidofältsformat måste du ge huvudlinjens innehåll högre synlighet än sidofältets innehåll.  

Inom varje grupp (huvudlinje eller sidofält) identifierar matrisen [Objekt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) den ordning som innehållet måste visas i. Varje objekt innehåller följande två sätt att identifiera resultatet i ett svar.  

-   `answerType`och `resultIndex` — `answerType` Fältet identifierar svaret (till exempel Webbsida `resultIndex` eller Nyheter) och identifierar ett resultat i svaret (till exempel en nyhetsartikel). Indexet är nollbaserat.  

-   `value`— `value` Fältet innehåller ett ID som matchar ID:t för antingen ett svar eller ett resultat i svaret. Antingen svaret eller resultaten innehåller ID men inte båda.  

Det är enklare att använda ID:t eftersom du bara behöver matcha ranknings-ID:t med ID:t för ett svar eller något av dess resultat. Om ett svarsobjekt innehåller ett `id` fält visar du alla svarsresultat tillsammans. Om objektet `News` till exempel `id` innehåller fältet visar du alla nyhetsartiklar tillsammans. Om `News` objektet inte innehåller `id` fältet innehåller varje nyhetsartikel `id` ett fält och rankningssvaret blandar nyhetsartiklarna med resultaten från andra svar.  

Använda `answerType` och `resultIndex` är lite mer komplicerat. Du `answerType` använder för att identifiera svaret som innehåller de resultat som ska visas. Sedan använder `resultIndex` du för att indexera genom svarets resultat för att få resultatet att visas. (Värdet `answerType` är namnet på fältet i [SearchResponse-objektet.)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) Om du ska visa alla svarsresultat tillsammans innehåller svarsobjektet inte `resultIndex` fältet.  

## <a name="ranking-response-example"></a>Exempel på rankningssvar

Följande visar ett exempel [på RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Eftersom webbsvaret inte `id` innehåller något fält visas alla webbsidor individuellt baserat på rangordningen (varje webbsida innehåller ett `id` fält). Och eftersom bilderna, videorna och relaterade `id` sökningar svaren innehåller fältet, visar du resultaten av vart och ett av dessa svar tillsammans baserat på rangordningen.

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
    }],  
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

Baserat på det här rankningssvaret visar huvudlinjen följande sökresultat:  

-   Det första webbsidans resultat
-   Alla bilder  
-   Den andra och tredje webbsidan resultat  
-   Alla videor  
-   Den 4: e, 5: e och 6: e webbsidan resultat  

Och sidofältet skulle visa följande sökresultat:  

-   Alla relaterade sökningar  


## <a name="next-steps"></a>Nästa steg

Information om hur du marknadsför orankade resultat finns i [Marknadsför svar som inte rankas](./filter-answers.md#promoting-answers-that-are-not-ranked).

Information om hur du begränsar antalet rangordnade svar i svaret finns [i Begränsa antalet svar i svaret](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Ett C#-exempel som använder rankning för att visa resultat finns i [självstudiekursen C#ranking](./csharp-ranking-tutorial.md).
