---
title: Använda rangordning för att visa web svaren | Microsoft Docs
description: Visar hur du använder rangordning för att visa svaren som returnerar Bing webb-API för sökning.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 750146f3bb28b94594a71733b68f092880360c5a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354570"
---
# <a name="using-ranking-to-display-results"></a>Med rangordning för att visa resultat  

Varje Sök-svaret innehåller en [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) svar, som anger hur visas i sökresultaten. Rangordning svaret grupperar resultaten av mainline innehåll och sidopanelen innehåll för en traditionell sökresultat sidan. Om du inte vill visa resultat i en traditionell likriktade och sidopanelen format måste du ange likriktade innehåll högre synlighet än sidopanelen innehåll.  
  
Inom varje grupp (mainline eller sidopanelen), [objekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matris identifierar den ordning som innehållet måste visas i. Varje post innehåller följande två sätt att identifiera resultatet i ett svar.  
  
-   `answerType` och `resultIndex` – den `answerType` fältet identifierar svar (till exempel webbsidan eller News) och `resultIndex` identifierar ett resultat i svaret (till exempel nyhetsartikel). Indexet är noll baserat.  
  
-   `value` – Det `value` fält innehåller ett ID som matchar ID för svar eller ett resultat i svaret. Svaret eller resultatet innehålla ID men inte båda.  
  
Det är enklare att använda eftersom du behöver bara matchar rangordning ID med ID i ett svar eller något av dess resultat med ID: T. Om ett svar-objekt innehåller ett `id` fältet, visa alla svar resultat tillsammans. Till exempel om den `News` objektet innehåller de `id` fältet, visas alla nyhetsartiklar tillsammans. Om den `News` objektet innehåller inte den `id` fältet varje nyheter artikeln innehåller en `id` fältet och rangordning svaret kombinerar nyhetsartiklar med resultaten från andra svar.  
  
Med hjälp av den `answerType` och `resultIndex` är lite mer komplicerad. Du använder `answerType` att identifiera det svar som innehåller resultat att visa. Sedan kan du använda `resultIndex` till index via svarets resultat för att få resultat att visa. (Den `answerType` värdet är namnet på fältet i den [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objekt.) Om du ska visa resultat som alla svar tillsammans rangordning svaret objektet innehåller de `resultIndex` fältet.  

## <a name="ranking-response-example"></a>Rangordning svar-exempel

Följande är ett exempel [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse). Eftersom Web-svaret inte innehåller en `id` fältet Visa alla webbsidor individuellt baserat på rangordning (varje webbsidan innehåller en `id` fält). Och eftersom bilder, videor och relaterade sökningar svar inkluderar den `id` fältet, visar resultaten av var och en av dessa tillsammans svar, baserat på rangordning.
  
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
  
Baserat på rangordning svaret visar på likriktade följande sökresultaten:  
  
-   Det första resultatet för webbsida 
-   Alla avbildningar  
-   Andra och tredje webbsidan resultaten  
-   Alla videor  
-   4, 5 och 6 webbsidan resultaten  
  
Och sidopanelen visas följande sökresultaten:  
  
-   Alla relaterade sökningar  
  

## <a name="next-steps"></a>Nästa steg

Information om att främja unranked resultat finns [befordrar svar som inte rangordnas](./filter-answers.md#promoting-answers-that-are-not-ranked).

Information om hur du begränsar antalet rangordnas svar i svaret finns [begränsning av antalet svar i svaret](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

En C# exempel som använder rangordning för att visa resultat som finns i [C# rangordning kursen](./csharp-ranking-tutorial.md).