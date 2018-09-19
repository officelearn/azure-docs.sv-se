---
title: Hur du bläddrar igenom de tillgängliga avbildningarna - bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att bläddra igenom alla avbildningar som kan returnera Bing.
services: cognitive-services
author: swhite-msft
manager: cgonlun
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 019d91f6a86bab5c4f446085e0244f9b5323f1fb
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294415"
---
# <a name="paging-results"></a>Växla resultat

När du anropar API: et för avbildning Search returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. För att få det uppskattade totala antalet tillgängliga resultat kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) fält.  

I följande exempel visas den `totalEstimatedMatches` fält som innehåller bilder svar.  

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  

Om du vill bläddra igenom de tillgängliga avbildningarna, använda den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) Frågeparametrar.  

Den `count` parametern anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 150. Standardvärdet är 35. Det faktiska talet som levereras kan vara mindre än vad som begärts.

Den `offset` parametern anger antalet resultat som hoppas över. Den `offset` är nollbaserat och måste vara mindre än (`totalEstimatedMatches` - `count`).  

Om du vill visa 20 bilder per sida, skulle du ställa in `count` till 20 och `offset` på 0 för att hämta den första sidan i resultaten. Nedan visas ett exempel som begär 20 bilder som börjar vid förskjutningen 40.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Om standard `count` värdet som passar din implementering, behöver du bara ange den `offset` frågeparameter.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Du kan förvänta dig att om du sidan 35 bilder i taget du skulle ställa in den `offset` frågeparameter till 0 på din första begäran och sedan öka `offset` av 35 för varje efterföljande begäran. Några av resultat i det efterföljande svaret kan dock vara dubbletter i föregående svar. De första två bilderna i svaret kan exempelvis vara samma som de två sista bilderna från föregående svar.

För att minimera duplicerade resultat måste använda den [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) i den `Images` objekt. Den `nextOffset` fältet får du den `offset` ska användas för nästa förfrågan. Till exempel om du vill att sidan 30 bilder i taget, du kan ange `count` till 30 och `offset` till 0 på din första begäran. I nästa förfrågan, anger du `count` till 30 och `offset` till värdet för föregående svar `nextOffset`. Om du vill bläddra bakåt föreslår vi att underhålla en stack med föregående förskjutningar och dyker den senaste.

> [!NOTE]
> Växling gäller endast för bildsökning (/ bilder/Sök) och inte på information om bilder eller populära bilder (/ bilder/trender).
