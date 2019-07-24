---
title: Sida via tillgängliga webb sidor – Anpassad sökning i Bing
titleSuffix: Azure Cognitive Services
description: Visar hur du kan gå igenom alla webb sidor som Anpassad sökning i Bing kan returnera.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 13b4cef624c636b8935897338badf3349f27c7f5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405041"
---
# <a name="paging-webpages"></a>Sid indelning för webb sidor 

När du anropar API för anpassad sökning returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som kan vara relevanta för frågan. Om du vill få det uppskattade totala antalet tillgängliga resultat kan du komma åt svars objektets [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) -fält.  
  
I följande exempel visas `totalEstimatedMatches` fältet som ett webb svar innehåller.  
  
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
  
Om du vill bläddra igenom de tillgängliga webb sidorna använder du parametrarna [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset) Query.  
  
`count` Parametern anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 50. Standardvärdet är 10. Det faktiska antalet som levereras kan vara mindre än begärt.

`offset` Parametern anger antalet resultat som ska hoppas över. Är noll-baserat och måste vara mindre än - (`count``totalEstimatedMatches`). `offset`  
  
Om du vill visa 15 webb sidor per sida, anger `count` du till 15 och `offset` 0 för att få den första resultat sidan. För varje efterföljande sida ökar `offset` du med 15 (till exempel 15, 30).  
  
Följande visar ett exempel som begär 15 webb sidor som börjar vid förskjutningen 45.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Om standardvärdet fungerar för din implementering behöver du bara `offset` ange Frågeparametern. `count`  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> `TotalEstimatedAnswers` Fältet är en uppskattning av det totala antalet Sök resultat som du kan hämta för den aktuella frågan.  När du ställer `count` in `offset` och parametrar `TotalEstimatedAnswers` kan antalet ändras. 

