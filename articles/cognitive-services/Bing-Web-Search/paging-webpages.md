---
title: Hur du bläddrar igenom sökresultat - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att bläddra igenom sökresultat från Bing Web Search API.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: erhopf
ms.openlocfilehash: e98407dface348dfdbc99a1146d50c46f3bb03a7
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739251"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Hur du bläddrar igenom resultaten från API för webbsökning i Bing

När du anropar API för webbsökning i Bing returnerar en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. För att få det uppskattade totala antalet tillgängliga resultat kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) fält.  

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

Om du vill bläddra igenom tillgängliga webbsidor, använda den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) Frågeparametrar.  

Den `count` parametern anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 50. Standardvärdet är 10. Det faktiska talet som levereras kan vara mindre än vad som begärts.

Den `offset` parametern anger antalet resultat som hoppas över. Den `offset` är nollbaserat och måste vara mindre än (`totalEstimatedMatches` - `count`).  

Om du vill visa 15 webbsidor per sida, skulle du ställa in `count` till 15 och `offset` på 0 för att hämta den första sidan i resultaten. För varje efterföljande sida du vill öka `offset` av 15 (till exempel 15, 30).  

I följande exempel begär 15 webbsidor som börjar vid förskjutningen 45.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Om standard `count` värdet som passar din implementering, behöver du bara ange den `offset` frågeparameter.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

API för webbsökning returnerar resultat som innehåller webbsidor och kan innehålla bilder, videor och nyheter. När du sidan sökresultaten växling i [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) svar och inte andra svar, till exempel bilder eller nyheter. Exempel: Om du ställer in `count` till 50, du kommer tillbaka 50 webbsidan resultat, men svaret kan innehålla resultat för de andra svar. Svaret kan exempelvis omfatta 15 avbildningar och 4 nyhetsartiklar. Det är också möjligt att resultaten kan innehålla Nyheter på första sidan, men inte den andra sidan, eller tvärtom.   

Om du anger den `responseFilter` frågeparameter och omfattar inte webbsidor i filterlistan kan inte använda den `count` och `offset` parametrar. 

> [!NOTE]
> Den `TotalEstimatedAnswers` fält är en uppskattning av det totala antalet sökresultat som du kan hämta för den aktuella frågan.  När du ställer in `count` och `offset` parametrar, den `TotalEstimatedAnswers` tal kan ändras. 
