---
title: Hur vill bläddra igenom tillgängliga avbildningar | Microsoft Docs
description: Visar hur du kan bläddra igenom alla avbildningar som Bing kan returnera.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a74ee817e84be5bb563c5fdaf25afc1dc14732e5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351387"
---
# <a name="paging-results"></a>Växla resultat

När du anropar avbildningen Sök-API, returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. För att få det totala antalet tillgängliga resultaten kan komma åt objektet svar [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) fältet.  
  
I följande exempel visas den `totalEstimatedMatches` fält som innehåller bilder svar.  
  
```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  
  
Om du vill bläddra igenom tillgängliga avbildningar, Använd den [antal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) och [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) fråga parametrar.  
  
Den `count` parameter anger antalet resultat som ska returneras i svaret. Det maximala antalet resultat som du kan begära i svaret är 150. Standardvärdet är 35. Det faktiska antalet levereras kan vara mindre än begärt.

Den `offset` parameter anger antalet resultat som ska hoppas över. Den `offset` är nollbaserade och bör vara mindre än (`totalEstimatedMatches` - `count`).  
  
Om du vill visa 20 bilder per sida kan du ställa in `count` till 20 och `offset` till 0 för att hämta den första sidan i resultaten. Nedan visas ett exempel som begär 20 bilder som börjar vid förskjutningen 40.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Om standard `count` värde fungerar för din implementering, måste du ange den `offset` Frågeparametern.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Du kan förvänta sig att du sidan 35 bilder i taget du vill ange om den `offset` fråga parametern till 0 på din första begäran och sedan öka `offset` av 35 för varje efterföljande begäran. Vissa resultat i efterföljande svaret kan dock finnas dubbletter av föregående svar. De två första bilderna i svaret kan till exempel vara samma som de två sista bilderna från föregående svar.

För att undvika dubbla resultat, Använd den [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) i den `Images` objekt. Den `nextOffset` fältet får du den `offset` ska användas för nästa begäran. Till exempel om du vill att sidan 30 bilder samtidigt, du anger `count` till 30 och `offset` till 0 på din första begäran. Du kan ange i din nästa förfrågan kan `count` till 30 och `offset` till värdet för föregående svar `nextOffset`. Om du vill bläddra bakåt rekommenderar vi att upprätthålla en hög tidigare förskjutningar och poppar den senaste.

> [!NOTE]
> Sidindelning gäller endast för image-sökning (/ bilder/söka) och inte till avbildningen insikter eller trender bilder (/ bilder/trender).