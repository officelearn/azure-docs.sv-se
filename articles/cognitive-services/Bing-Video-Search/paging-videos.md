---
title: Hur du bläddrar igenom tillgängliga videor - Videosökning
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att bläddra igenom alla videor som returneras av den Videosökning i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 0af36fa68b2d801eed52e6f081b040fb56929c91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613650"
---
# <a name="paging-through-video-search-results"></a>Växla genom videosökningsresultat

Bing-Videosökning returnerar en delmängd av alla sökresultat hittades för din fråga. Av bläddring genom de resultaten med efterföljande anrop till API: et kan du hämta och visa dem i ditt program.

> [!NOTE]
> Växling gäller endast för videor search (Sök/filmer /) och inte på videoinsikter (information om/filmer /) eller populära videorna (/ filmer /).

## <a name="total-estimated-matches"></a>Totala uppskattade matchningar

Hämta det uppskattade antalet sökresultat hittades med den [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) i JSON-svar.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Växla genom videor

Om du vill bläddra igenom tillgängliga videor, använda den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) frågeparametrar när du skickar din begäran.  
  

|Parameter  |Beskrivning  |
|---------|---------|
|`count`     | Anger hur många resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 100. Standardvärdet är 10. Det faktiska talet som levereras kan vara mindre än vad som begärts.        |
|`offset`     | Anger hur många resultat som hoppas över. Den `offset` är nollbaserat och måste vara mindre än (`totalEstimatedMatches` - `count`).          |

Till exempel om du vill visa 20 artiklar per sida du vill ange `count` till 20 och `offset` på 0 för att hämta den första sidan i resultaten. För varje efterföljande sida du vill öka `offset` med 20 (till exempel 20, 40).  
  
I följande exempel begär 20 videor som börjar vid förskjutningen 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Om du använder standardvärdet för den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count), behöver du bara ange den `offset` frågeparameter som i följande exempel.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Om du bläddra igenom 35 videor i taget, anger du den `offset` frågeparameter till 0 på din första begäran och sedan öka `offset` av 35 för varje efterföljande begäran. Vissa resultat i nästa svaret kan dock innehålla dubbla video resultaten från föregående svar. De första två videorna i ett svar kan exempelvis vara samma som de två sista videorna från föregående svar.

För att minimera duplicerade resultat måste använda den [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) i den `Videos` objekt.

Till exempel om du vill att sidan 30 videor i taget, du kan ange `count` till 30 och `offset` till 0 på din första begäran. I nästa förfrågan, anger du den `offset` frågeparameter till den `nextOffset` värde.

> [!NOTE]
> Den `TotalEstimatedAnswers` fält är en uppskattning av det totala antalet sökresultat som du kan hämta för den aktuella frågan.  När du ställer in `count` och `offset` parametrar, den `TotalEstimatedAnswers` tal kan ändras. 
  
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta insikter om videor](video-insights.md)
