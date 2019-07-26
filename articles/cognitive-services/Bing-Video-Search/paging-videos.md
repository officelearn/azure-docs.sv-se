---
title: Så här bläddrar du igenom tillgängliga videor – Videosökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du bläddrar igenom alla videor som returneras av API för videosökning i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c3570d8772734595c6707ca8103006867a8eb47a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500711"
---
# <a name="paging-through-video-search-results"></a>Växla mellan video Sök Resultat

API för videosökning i Bing returnerar en delmängd av alla Sök resultat som hittades för frågan. Genom att växla genom dessa resultat med efterföljande anrop till API: et kan du hämta och visa dem i ditt program.

> [!NOTE]
> Sid indelning gäller endast videor search (/Videos/search) och inte video Insights (/Videos/Details) eller Trends ökningar (/Videos/trending).

## <a name="total-estimated-matches"></a>Totalt antal uppskattade matchningar

Använd fältet [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) i JSON-svaret för att få det beräknade antalet hittade Sök resultat.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Växla genom videor

Om du vill bläddra igenom de tillgängliga videorna använder du parametrarna count and offset Query ( [räkna](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) och [Förskjut](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) ) när du skickar din begäran.  
  

|Parameter  |Beskrivning  |
|---------|---------|
|`count`     | Anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 100. Standardvärdet är 10. Det faktiska antalet som levereras kan vara mindre än begärt.        |
|`offset`     | Anger antalet resultat som ska hoppas över. Är noll-baserat och måste vara mindre än - (`count``totalEstimatedMatches`). `offset`          |

Om du till exempel vill visa 20 artiklar per sida, anger `count` du 20 och `offset` till 0 för att få den första resultat sidan. För varje efterföljande sida skulle du öka `offset` med 20 (till exempel 20, 40).  
  
I följande exempel begärs 20 videor, med början vid förskjutningen 40.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Om du använder standardvärdet för [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count), behöver du bara ange `offset` Frågeparametern, som i följande exempel.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Om du går igenom 35-videor i taget anger `offset` du Frågeparametern till 0 för din första begäran och ökar `offset` sedan med 35 på varje efterföljande begäran. Vissa resultat i nästa svar kan dock innehålla dubbla video resultat från föregående svar. De två första videor i ett svar kan till exempel vara samma som de två sista videor från föregående svar.

Använd [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) -fältet för `Videos` objektet för att eliminera dubblerade resultat.

Om du till exempel vill placera 30 videor i taget kan du ange `count` 30 och `offset` 0 som första förfrågan. I nästa förfrågan anger `offset` du Frågeparametern `nextOffset` till värdet.

> [!NOTE]
> `TotalEstimatedAnswers` Fältet är en uppskattning av det totala antalet Sök resultat som du kan hämta för den aktuella frågan.  När du ställer `count` in `offset` och parametrar `TotalEstimatedAnswers` kan antalet ändras. 
  
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta insikter om videor](video-insights.md)
