---
title: Hur du bläddrar igenom resultaten för nyhetssökning i Bing
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att bläddra igenom nyhetsartiklar som returnerar de nyhetssökning i Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 9acf0c26707dab9de443b06e7314de6d77913777
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258262"
---
# <a name="how-to-page-through-news-search-results"></a>Hur du bläddrar igenom nyhetssökningsresultat

När du anropar API: et för Nyheter Search returnerar Bing en lista med resultat som är relevanta för din fråga. För att få det uppskattade totala antalet tillgängliga resultat kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) fält.  
  
I följande exempel visas den `totalEstimatedMatches` fält som innehåller ett nytt svar.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Om du vill bläddra igenom tillgängliga artiklar, använda den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset) Frågeparametrar.  
 

|Parameter  |Beskrivning  |
|---------|---------|
|`count`     | Anger hur många resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 100. Standardvärdet är 10. Det faktiska talet som levereras kan vara mindre än vad som begärts.        |
|`offset`     | Anger hur många resultat som hoppas över. Den `offset` är nollbaserat och måste vara mindre än (`totalEstimatedMatches` - `count`).          |

Till exempel om du vill visa 20 artiklar per sida du vill ange `count` till 20 och `offset` på 0 för att hämta den första sidan i resultaten. För varje efterföljande sida du vill öka `offset` med 20 (till exempel 20, 40).  
  
I följande exempel begär 20 nyhetsartiklar som börjar vid förskjutningen 40.  

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

> [!NOTE]
> Den `TotalEstimatedAnswers` fält är en uppskattning av det totala antalet sökresultat som du kan hämta för den aktuella frågan.  När du ställer in `count` och `offset` parametrar, den `TotalEstimatedAnswers` tal kan ändras. 