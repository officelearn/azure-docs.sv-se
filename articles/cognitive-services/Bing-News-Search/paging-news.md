---
title: Hur du bläddrar igenom de tillgängliga nyhetsartiklar - nyhetssökning i Bing
titlesuffix: Azure Cognitive Services
description: Visar hur du bläddra igenom alla nyhetsartiklar som ny sökning i Bing kan returnera.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: fff1da15df2e690cd0b37bb82654a4d30159325a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803400"
---
# <a name="paging-news"></a>Växling Nyheter

När du anropar API: et för Nyheter Search returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. För att få det uppskattade totala antalet tillgängliga resultat kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) fält.  
  
I följande exempel visas den `totalEstimatedMatches` fält som innehåller ett nytt svar.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Om du vill bläddra igenom tillgängliga artiklar, använda den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) Frågeparametrar.  
  
Den `count` parametern anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 100. Standardvärdet är 10. Det faktiska talet som levereras kan vara mindre än vad som begärts.

Den `offset` parametern anger antalet resultat som hoppas över. Den `offset` är nollbaserat och måste vara mindre än (`totalEstimatedMatches` - `count`).  

Om du vill visa 20 artiklar per sida, skulle du ställa in `count` till 20 och `offset` på 0 för att hämta den första sidan i resultaten. För varje efterföljande sida du vill öka `offset` med 20 (till exempel 20, 40).  
  
Nedan visas ett exempel som begär 20 nyhetsartiklar som börjar vid förskjutningen 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Om standard `count` värde fungerar för din implementering, bara ange den `offset` frågeparameter som visas i följande exempel:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Växling gäller endast för nyhetssökning (/ news/Sök) och inte på populära ämnen (/ news/trendingtopics) eller nyhetskategorier (/ nyheter).