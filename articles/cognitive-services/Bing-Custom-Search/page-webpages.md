---
title: Bläddrar igenom tillgängliga webbsidor – Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Visar hur du bläddra igenom alla webbsidor som Bing Custom Search kan returnera.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 891d0f88158b7d315b5908a7e1c0f73215b4b09e
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235249"
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

> [!NOTE]
> Den `TotalEstimatedAnswers` fält är en uppskattning av det totala antalet sökresultat som du kan hämta för den aktuella frågan.  När du ställer in `count` och `offset` parametrar, den `TotalEstimatedAnswers` tal kan ändras. 

