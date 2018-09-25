---
title: 'Anpassad sökning i Bing: Bläddra igenom tillgängliga webbsidor | Microsoft Docs'
description: Visar hur du bläddra igenom alla webbsidor som Bing kan returnera.
services: cognitive-services
author: brapel
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 5692776736090c55547c2fe934e2c0aaf840d3c5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982366"
---
# <a name="paging-webpages"></a>Växling webbsidor 

När du anropar API: et för anpassad sökning i Bing returnerar en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. För att få det uppskattade totala antalet tillgängliga resultat kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) fält.  
  
I följande exempel visas den `totalEstimatedMatches` fält som innehåller en Web-svar.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Om du vill bläddra igenom tillgängliga webbsidor, använda den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset) Frågeparametrar.  
  
Den `count` parametern anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 50. Standardvärdet är 10. Det faktiska talet som levereras kan vara mindre än vad som begärts.

Den `offset` parametern anger antalet resultat som hoppas över. Den `offset` är nollbaserat och måste vara mindre än (`totalEstimatedMatches` - `count`).  
  
Om du vill visa 15 webbsidor per sida, skulle du ställa in `count` till 15 och `offset` på 0 för att hämta den första sidan i resultaten. För varje efterföljande sida du vill öka `offset` av 15 (till exempel 15, 30).  
  
Nedan visas ett exempel som begär 15 webbsidor som börjar vid förskjutningen 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Om standard `count` värdet som passar din implementering, behöver du bara ange den `offset` frågeparameter.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

