---
title: Hur vill bläddra igenom tillgängliga nyhetsartiklar | Microsoft Docs
description: Visar hur du kan bläddra igenom alla nyhetsartiklar som Bing kan returnera.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EA388F72-FA43-493B-967C-9560B3243C62
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2c90d468536f0864d7deac073667e29e9a54692f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351465"
---
# <a name="paging-news"></a>Nyheter för sidindelning

När du anropar nyheter Sök-API, returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevant för frågan. För att få det totala antalet tillgängliga resultaten kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) fältet.  
  
I följande exempel visas den `totalEstimatedMatches` fält som inkluderar svar en andra gång.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Om du vill bläddra igenom tillgängliga artiklar, Använd den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) fråga parametrar.  
  
Den `count` parameter anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 100. Standardvärdet är 10. Det faktiska antalet levereras kan vara mindre än begärt.

Den `offset` parameter anger antalet resultat som ska hoppas över. Den `offset` är nollbaserade och bör vara mindre än (`totalEstimatedMatches` - `count`).  

Om du vill visa 20 artiklar per sida kan du ställa in `count` till 20 och `offset` till 0 för att hämta den första sidan i resultaten. För varje efterföljande sida som du vill öka `offset` med 20 (till exempel 20, 40).  
  
Nedan visas ett exempel som begär 20 nyhetsartiklar som börjar vid förskjutningen 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Om standard `count` värde fungerar för din implementering, ange bara den `offset` Frågeparametern som visas i följande exempel:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Sidindelning gäller endast för Nyheter search (Sök-diskussionsgrupper) och inte till trender avsnitt (/ nyheter/trendingtopics) eller nyhetskategorier (/ nyheter).