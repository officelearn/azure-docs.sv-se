---
title: Sök på webben efter populära videor med hjälp av API:et för videosökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API:et för videosökning på Bing för att söka på webben efter populära videor.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: ea6f79bf6c305ecc07b3e684ede15ad439039e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500620"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>Få populära videor med API:et för videosökning på Bing 

Med API:et för videosökning på Bing kan du hitta dagens populära videor från hela webben och i olika kategorier. 

## <a name="get-request"></a>HÄMTA begäran

Om du vill hämta dagens populära videor från API:et för videosökning på Bing skickar du följande GET-begäran:  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>Marknadsstöd

Följande marknader stöder populära videor.  
 
-   sv-AU (engelska, Australien)  
-   sv-CA (Engelska, Kanada)  
-   en-GB (engelska, Storbritannien)  
-   sv-ID (Engelska, Indonesien)  
-   sv-IE (engelska, Irland)  
-   sv-IN (engelska, Indien)  
-   en-NZ (Engelska, Nya Zeeland)  
-   sv-PH (engelska, Filippinerna)  
-   en-SG (engelska, Singapore)  
-   sv-US (engelska, USA)  
-   sv-WW (engelska, global samlingskod)  
-   sv-ZA (engelska, Sydafrika)  
-   zh-CN (kinesiska, Kina)

## <a name="example-json-response"></a>Exempel på JSON-svar  

I följande exempel visas ett API-svar som innehåller populära videor, som visas efter kategori och underkategori. Svaret innehåller också bannervideor, som är de mest populära populära videorna, och kan komma från en eller flera kategorier.  

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