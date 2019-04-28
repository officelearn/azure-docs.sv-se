---
title: Sök efter populära videor med Bing-Videosökning på webben
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder den Videosökning i Bing för att söka efter trendande videor på webben.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 486cf2e3bcf851f23011bb2fb8d91691d6190698
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431930"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Hämta trendande videor med Bing-Videosökning 

Bing-Videosökning kan du hitta dagens populära videor från på webben och i olika kategorier. 

## <a name="get-request"></a>HÄMTNING av begäran

Skicka följande GET-begäran för att få dagens populära videor från Bing-Videosökning:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Stöd för marknaden

Följande marknader stöder trendande videor.  
 
-   en AU (engelska, Australien)  
-   en – USA (engelska, Kanada)  
-   en-GB (engelska, Storbritannien)  
-   SV-ID (engelska, Indonesien)  
-   en IE (engelska, Irland)  
-   SV-Indien (engelska, Indien)  
-   en NZ (engelska, Nya Zeeland)  
-   en f (engelska, Filippinerna)  
-   en-SG (English, Singapore)  
-   en-US (engelska, USA)  
-   en WW (engelska, Worldwide aggregera kod)  
-   SV-ZA (engelska, Sydafrika)  
-   zh-CN (kinesiska, Kina)

## <a name="example-json-response"></a>Exempel på JSON-svar  

I följande exempel visas ett API-svar som innehåller populära videor som är listade efter kategori och underkategori. Svaret innehåller även banderoll videor som är mest populära trendande videor och kan komma från en eller flera kategorier.  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta insikter om videor](video-insights.md)