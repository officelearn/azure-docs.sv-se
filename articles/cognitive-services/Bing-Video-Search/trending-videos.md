---
title: Söka på webben efter trender videor | Microsoft Docs
description: Visar hur du använder Bing Video Sök API för att söka på webben efter trender videor.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 897A28A3-0980-484E-814F-FFE1D5C885E6
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8db7fcf77042631260b4b165bd3d44053827f3ce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351417"
---
# <a name="get-trending-videos"></a>Hämta trender videor  

Skicka följande GET-begäran för att få dagens trender videor:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Följande marknader stöd trender videoklipp.  
 
-   SV-Australien (engelska, Australien)  
-   SV-CA (engelska, Kanada)  
-   en-GB (engelska, Storbritannien)  
-   SV-ID (engelska, Indonesien)  
-   SV-IE (engelska, Irland)  
-   SV-IN (engelska, Indien)  
-   SV-NZ (engelska, Nya Zeeland)  
-   SV-PH (engelska, Filippinerna)  
-   SV-SG (engelska, Singapore)  
-   en-US (engelska, USA)  
-   SV WW (engelska, Worldwide aggregera kod)  
-   SV-ÖA (engelska, Sydafrika)  
-   zh-CN (kinesiska, Kina)

  
I följande exempel visas ett svar som innehåller trender videor.  

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
Svaret innehåller en lista över videor efter kategori och underkategori. Om listan över kategorier som finns på en kategori för musik, videor och en av dess underkategorier var upp, kan du exempelvis skapa en upp musikvideor kategori i användarupplevelsen. Du kan sedan använda den `thumbnailUrl`, `displayText`, och `webSearchUrl` fält för att skapa en klickbar panel under varje kategori (till exempel upp musikvideor). När användaren klickar på ikonen förflyttas de till Bing's video webbläsare där videon spelas upp.

Svaret innehåller också banderoll videor, vilka är de mest populära trender videorna. Banderoll videor kan komma från en eller flera kategorier.  
  
