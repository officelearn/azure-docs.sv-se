---
title: Så här bläddrar du genom Nyhetssökning i Bing resultat
titleSuffix: Azure Cognitive Services
description: Lär dig hur du bläddrar bland nyhets artiklarna som API för nyhetssökning i Bing returnerar.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: e5f8dce1a03e44758eea737ad2da419fa67c36a2
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423742"
---
# <a name="how-to-page-through-news-search-results"></a>Hur du bläddrar bland nyhets Sök Resultat

När du anropar Nyhetssökning API returnerar Bing en lista med resultat som är relevanta för din fråga. Om du vill få det uppskattade totala antalet tillgängliga resultat kan du komma åt svars objektets [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) -fält.  
  
I följande exempel visas `totalEstimatedMatches` fältet som ett nyhets svar innehåller.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Du kan bläddra igenom de tillgängliga artiklarna med parametrarna [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset) Query.  
 

|Parameter  |Beskrivning  |
|---------|---------|
|`count`     | Anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 100. Standardvärdet är 10. Det faktiska antalet som levereras kan vara mindre än begärt.        |
|`offset`     | Anger antalet resultat som ska hoppas över. Är noll-baserat och måste vara mindre än - (`count``totalEstimatedMatches`). `offset`          |

Om du till exempel vill visa 20 artiklar per sida, anger `count` du 20 och `offset` till 0 för att få den första resultat sidan. För varje efterföljande sida skulle du öka `offset` med 20 (till exempel 20, 40).  
  
I följande exempel begärs 20 nyhets artiklar som börjar vid förskjutningen 40.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Om standardvärdet fungerar för din implementering anger du `offset` bara Frågeparametern som visas i följande exempel: `count`  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Sid indelning gäller enbart för nyhets sökning (/News/search) och inte för att hitta ämnen (/News/trendingtopics) eller nyhets kategorier (/News).

> [!NOTE]
> `TotalEstimatedAnswers` Fältet är en uppskattning av det totala antalet Sök resultat som du kan hämta för den aktuella frågan.  När du ställer `count` in `offset` och parametrar `TotalEstimatedAnswers` kan antalet ändras. 
