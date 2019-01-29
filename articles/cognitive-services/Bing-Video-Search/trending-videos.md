---
title: Sök efter populära videor – Videosökning i Bing på webben
titlesuffix: Azure Cognitive Services
description: Visar hur du använder den Videosökning i Bing för att söka på webben för trendande videor.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 71dd888199e9e630835c4916d35f6308431bab62
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203563"
---
# <a name="get-trending-videos"></a>Hämta populära videor  

Skicka följande GET-begäran för att få dagens populära videor:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

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

  
I följande exempel visas ett svar som innehåller trendande videor.  

```  
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
Svaret innehåller en lista med videor efter kategori och underkategori. Om listan över kategorier som innehöll en kategori för musik, videor och en av dess underkategorier var upp, kan du till exempel skapa en kategori för Top musikvideor i din användarupplevelse. Du kan sedan använda den `thumbnailUrl`, `displayText`, och `webSearchUrl` fält för att skapa en klickbar panelen under varje kategori (till exempel upp musikvideor). När användaren klickar på panelen, kommer de till Bings video webbläsare där videon spelas upp.

Svaret innehåller även banderoll videor, vilka är de mest populära och trendande videorna. Banderoll videor kan komma från en eller flera kategorier.  
  
