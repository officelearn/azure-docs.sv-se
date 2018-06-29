---
title: Använda rangordning för att visa svar | Microsoft Docs
description: Visar hur du använder rangordning för att visa svaren som returnerar Bing enheten Sök-API.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 53354c0f78419a37e8896bb4d00e0d7aebf32203
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060000"
---
# <a name="using-ranking-to-display-results"></a>Med rangordning för att visa resultat  

Varje entitet Sök svaret innehåller en [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) besvara, liknar det i ett Bing webbsökning svar, som anger hur visas i sökresultaten. Rangordning svaret grupperar resultaten i pol, likriktade, och sidopanelen innehåll. Pol resultatet är mest viktiga eller framträdande resultatet och ska visas först. Om du inte vill visa de återstående resulterar i en traditionell likriktade och sidopanelen format måste du ange likriktade innehåll högre synlighet än sidopanelen innehåll. 
  
I varje grupp i [objekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matris identifierar den ordning som innehållet måste visas i. Varje element finns två sätt att identifiera resultatet i ett svar.  
  
-   `answerType` och `resultIndex` – den `answerType` fältet identifierar svar (entiteten eller plats) och `resultIndex` identifierar ett resultat i svaret (till exempel en enhet). Indexet är noll baserat.  
  
-   `value` – Det `value` fält innehåller ett ID som matchar ID för svar eller ett resultat i svaret. Svaret eller resultatet innehålla ID men inte båda.  
  
Med ID: T måste du matchar rangordning ID med ID i ett svar eller något av dess resultat. Om ett svar-objekt innehåller ett `id` fältet, visa alla svar resultat tillsammans. Till exempel om den `Entities` objektet innehåller de `id` fältet, visas alla entiteter artiklar tillsammans. Om den `Entities` objektet innehåller inte den `id` fältet varje entitet innehåller en `id` fältet och rangordning svaret blandas entiteterna med platser resultaten.  
  
Med hjälp av den `answerType` och `resultIndex` är en tvåstegsprocess. Först måste du använda `answerType` att identifiera det svar som innehåller resultat att visa. Kan du använda `resultIndex` till index för att besvara resultat för att få resultat att visa. (Den `answerType` värdet är namnet på fältet i den [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objekt.) Om du ska visa resultat som alla svar tillsammans rangordning svaret objektet innehåller de `resultIndex` fältet.

## <a name="ranking-response-example"></a>Rangordning svar-exempel

Följande är ett exempel [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Baserat på rangordning svaret visas sidopanelen resultaten för två entitet som rör Jimi Hendrix.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Sök i Bing enheten självstudiekursen](tutorial-bing-entities-search-single-page-app.md)
