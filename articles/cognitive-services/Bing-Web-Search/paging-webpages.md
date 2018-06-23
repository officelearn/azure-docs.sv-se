---
title: Hur vill bläddra igenom de tillgängliga webbsidorna | Microsoft Docs
description: Visar hur du kan bläddra igenom alla webbsidor som Bing kan returnera.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: bf29783246c603270d59b20b63027fccdbd45b89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351528"
---
# <a name="paging-webpages"></a>Sidindelning webbsidor 

När du anropar Sök webb-API, returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevant för frågan. För att få det totala antalet tillgängliga resultaten kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) fältet.  
  
I följande exempel visas den `totalEstimatedMatches` fält som inkluderar ett webb-svar.  
  
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
  
Om du vill bläddra igenom de tillgängliga webbsidorna, Använd den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) fråga parametrar.  
  
Den `count` parameter anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 50. Standardvärdet är 10. Det faktiska antalet levereras kan vara mindre än begärt.

Den `offset` parameter anger antalet resultat som ska hoppas över. Den `offset` är nollbaserade och bör vara mindre än (`totalEstimatedMatches` - `count`).  
  
Om du vill visa 15 webbsidor per sida kan du ställa in `count` 15 och `offset` till 0 för att hämta den första sidan i resultaten. För varje efterföljande sida som du vill öka `offset` av 15 (till exempel 15, 30).  
  
I följande exempel begär 15 webbsidor som börjar vid förskjutningen 45.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Om standard `count` värde fungerar för din implementering, måste du ange den `offset` Frågeparametern.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Sök webb-API: returnerar resultat med webbsidor som kan inkludera bilder, videor och nyheter. När du sidan sökresultaten växling av [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) svar och inte andra svar, till exempel bilder eller news. Till exempel om du ställer in `count` till 50, åter 50 webbsidan resultat, men svaret kan inkludera resultat för de andra svar. Svaret kan exempelvis omfatta 15 bilder och 4 nyhetsartiklar. Det är också möjligt att resultaten kan inkludera Nyheter på första sidan men inte på den andra sidan, och vice versa.   
    
Om du anger den `responseFilter` Frågeparametern och inkluderar inte webbsidor i filterlistan kan inte använda den `count` och `offset` parametrar.  
