---
title: Söka på webben för trender bilder | Microsoft Docs
description: Visar hur du använder Bing bilder Sök API för att söka på webben för trender bilder.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b12524cd4c1896501820209b3a45746b8f38b210
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351474"
---
# <a name="get-trending-images"></a>Hämta trender bilder  

Skicka följande GET-begäran för att få dagens trender bilder:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

API: et för trender bilder stöder för närvarande endast följande marknader:  

- en-US (engelska, USA)  
- SV-CA (engelska, Kanada)  
- SV-Australien (engelska, Australien)  
- zh-CN (kinesiska, Kina)

Svaret innehåller en [TrendingImages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#trendingimages) objekt som innehåller bilder efter kategori. Använd kategorin `title` gruppering bilderna i användarupplevelsen. Kategorierna kan ändras varje dag.  
  
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
  
Varje bricka innehåller en avbildning och alternativ för att hämta relaterade bilder. Du kan använda frågan för att hämta relaterade bilder `text` att anropa den [avbildningen Sök-API](./search-the-web.md) och visa relaterade bilder själv. Du kan också använda URL: en i `webSearchUrl` att ta användaren till Bings bilder sökresultatsidan som innehåller relaterade bilder. 

Om du anropar avbildningen Sök-API för att hämta relaterade avbildningar, anger du den [id](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#id) Frågeparametern-ID: t i den `id` fältet. Anger ID säkerställer att svaret innehåller avbildningen (det är den första bilden i svaret) och dess relaterade bilder. Ange dessutom den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#q) Frågeparametern texten i den `query` objektets `text` fältet.

I följande exempel visas hur du använder avbildnings-ID för att hämta relaterade avbildningar av John Smith i det föregående trender bilder API-svaret.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  
