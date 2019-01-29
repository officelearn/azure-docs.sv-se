---
title: Hur du bläddrar igenom tillgängliga videor - Videosökning
titlesuffix: Azure Cognitive Services
description: Visar hur du bläddra igenom alla videor som Bing kan returnera.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 26e706b054653b8f0ad1ea14d0a9c2ca97cd227f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181800"
---
# <a name="paging-videos"></a>Växling videor

När du anropar API för videosökning i Bing returneras en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. För att få det uppskattade totala antalet tillgängliga resultat kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) fält.  
  
I följande exempel visas den `totalEstimatedMatches` fält som innehåller en Video-svar.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Om du vill bläddra igenom tillgängliga videor, använda den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) Frågeparametrar.  
  
Den `count` parametern anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 105. Standardvärdet är 35. Det faktiska talet som levereras kan vara mindre än vad som begärts.

Den `offset` parametern anger antalet resultat som hoppas över. Den `offset` är nollbaserat och måste vara mindre än (`totalEstimatedMatches` - `count`).  
  
Om du vill visa 20 videor per sida, skulle du ställa in `count` till 20 och `offset` på 0 för att hämta den första sidan i resultaten. För varje efterföljande sida du vill öka `offset` med 20 (till exempel 20, 40).  

Nedan visas ett exempel som begär 20 videor som börjar vid förskjutningen 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Om standard `count` värdet som passar din implementering, behöver du bara ange den `offset` frågeparameter.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Normalt om du sidan 35 videor i taget, anger du den `offset` frågeparameter till 0 på din första begäran och sedan öka `offset` av 35 för varje efterföljande begäran. Några av resultat i det efterföljande svaret kan dock vara dubbletter i föregående svar. De första två videorna i svaret kan exempelvis vara samma som de två sista videorna från föregående svar.

För att minimera duplicerade resultat måste använda den [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) i den `Videos` objekt.

Till exempel om du vill att sidan 30 videor i taget, du kan ange `count` till 30 och `offset` till 0 på din första begäran. I nästa förfrågan, anger du den `offset` frågeparameter till den `nextOffset` värde.


> [!NOTE]
> Växling gäller endast för videor search (Sök/filmer /) och inte på videoinsikter (information om/filmer /) eller populära videorna (/ filmer /).

> [!NOTE]
> Den `TotalEstimatedAnswers` fält är en uppskattning av det totala antalet sökresultat som du kan hämta för den aktuella frågan.  När du ställer in `count` och `offset` parametrar, den `TotalEstimatedAnswers` tal kan ändras. 
