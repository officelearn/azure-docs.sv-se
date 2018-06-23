---
title: Hur vill bläddra igenom de tillgängliga videorna | Microsoft Docs
description: Visar hur du kan bläddra igenom alla videor som Bing kan returnera.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 910A485F-BCF3-42B9-958D-DD48BDEDA965
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 00476825eb3fc1008c3f2172b591d8b7a2f35884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351432"
---
# <a name="paging-videos"></a>Sidindelning videor

När du anropar API: T för videon Sök returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. För att få det totala antalet tillgängliga resultaten kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) fältet.  
  
I följande exempel visas den `totalEstimatedMatches` fält som inkluderar en Video-svar.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Om du vill bläddra igenom de tillgängliga videorna, Använd den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) fråga parametrar.  
  
Den `count` parameter anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 105. Standardvärdet är 35. Det faktiska antalet levereras kan vara mindre än begärt.

Den `offset` parameter anger antalet resultat som ska hoppas över. Den `offset` är nollbaserade och bör vara mindre än (`totalEstimatedMatches` - `count`).  
  
Om du vill visa 20 videor per sida, skulle du ange `count` till 20 och `offset` till 0 för att hämta den första sidan i resultaten. För varje efterföljande sida som du vill öka `offset` med 20 (till exempel 20, 40).  

Nedan visas ett exempel som begär 20 videor som börjar vid förskjutningen 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Om standard `count` värde fungerar för din implementering, måste du ange den `offset` Frågeparametern.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Normalt om du sidan 35 videor i taget skulle anger du den `offset` fråga parametern till 0 på din första begäran och sedan öka `offset` av 35 för varje efterföljande begäran. Vissa resultat i efterföljande svaret kan dock finnas dubbletter av föregående svar. De två första videorna i svaret kan till exempel vara samma som de två sista videorna från föregående svar.

För att undvika dubbla resultat, Använd den [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) i den `Videos` objekt.

Till exempel om du vill att sidan 30 videor samtidigt, du anger `count` till 30 och `offset` till 0 på din första begäran. I nästa begäran, skulle du ställer in den `offset` Frågeparametern till den `nextOffset` värde.


> [!NOTE]
> Sidindelning gäller endast videor search (Sök-video) och inte för video insikter (/ videor/detaljer) eller trender videoklipp (/ videor/trender).