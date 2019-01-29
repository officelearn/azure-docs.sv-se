---
title: Hämta populära bilder med den bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Sök efter dagens populära bilder från webben med sökning i Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 7d79e81f8670492d025727ed76d7d529dbd4719d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188328"
---
# <a name="get-trending-images-from-the-web"></a>Visa populära bilder från webben

Skicka följande GET-begäran för att få dagens populära bilder:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

API: et för populära bilder stöder för närvarande endast följande marknader:  

- en-US (engelska, USA)  
- en – USA (engelska, Kanada)  
- en AU (engelska, Australien)  
- zh-CN (kinesiska, Kina)

Svaret innehåller en [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) objekt som visar en lista över avbildningar efter kategori. Använda kategorins `title` att gruppera avbildningar i din användarupplevelse. Kategorier kan ändras varje dag.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Varje panel innehåller en bild och alternativ för att hämta relaterade bilder. För att få de relaterade bilderna kan du använda frågan `text` att anropa den [Image Search API](./search-the-web.md) och visa de relaterade bilderna själv. Du kan också använda URL: en i `webSearchUrl` ska användaren att Bings avbildningar sökresultatsidan som innehåller relaterade bilder.

Om du anropar Image Sök-API för att hämta relaterade bilder, anger du den [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) frågeparameter med ID i den `id` fält. Anger ID säkerställer att svaret innehåller avbildningen (det är den första bilden i svaret) och dess relaterade bilder. Ange dessutom den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#q) frågeparameter i texten i den `query` objektets `text` fält.

I följande exempel visar hur du använder avbildnings-ID för att hämta relaterade bilder av John Smith i föregående populära bilder API-svaret.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
