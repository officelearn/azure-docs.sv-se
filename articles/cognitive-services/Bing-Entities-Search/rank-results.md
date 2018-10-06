---
title: Med hjälp av rangordning för att visa svar - Entitetssökning i Bing
titlesuffix: Azure Cognitive Services
description: Visar hur du använder rangordning för att visa de svar som returnerar de Entitetssökning i Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 4a336ccaea18ab84464f28aef170ccdc423b216d
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814601"
---
# <a name="using-ranking-to-display-results"></a>Med hjälp av rangordning för att visa resultat  

Varje entitet search svaret innehåller en [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) besvara, ungefär som i ett svar för webbsökning i Bing, som anger hur du måste visa sökresultatet. Rangordning svaret grupperar resultaten till pol, likriktade, och sidopanelen innehåll. Pol resultatet är mest viktigt eller framträdande resultatet och ska visas först. Om du inte vill visa de återstående resulterar i en traditionell likriktade och sidopanelen format, måste du ange likriktade innehåll högre synlighet än sidopanelen innehållet. 
  
Inom varje grupp i [objekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matris identifierar den ordning som innehållet måste finnas i. Varje objekt innehåller två metoder för att identifiera resultatet i ett svar.  
  
-   `answerType` och `resultIndex` – den `answerType` fältet identifierar svar (entitet eller plats) och `resultIndex` identifierar ett resultat i svaret (till exempel en entitet). Indexet är nollbaserat.  
  
-   `value` – Det `value` fält innehåller ett ID som matchar ID: T för ett svar eller ett resultat i svaret. Svaret eller resultatet innehålla ID men inte båda.  
  
Med hjälp av ID måste du matcha rangordning ID med ID: T för ett svar eller ett av resultaten. Om ett svar-objekt innehåller en `id` fältet, visa resultat som svaret finns tillsammans. Till exempel om den `Entities` objektet innehåller de `id` fältet, visa alla entiteter artiklar tillsammans. Om den `Entities` objekt omfattar inte den `id` fältet och sedan varje entitet innehåller en `id` fältet och svaret rangordning blandas entiteterna med platser resultaten.  
  
Med hjälp av den `answerType` och `resultIndex` är en tvåstegsprocess. Först måste du använda `answerType` att identifiera det svar som innehåller resultatet ska visas. Kan du använda `resultIndex` till index till den svar resultat att få resultat att visa. (Den `answerType` värdet är namnet på fältet i den [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objekt.) Om du ska visa resultat som svaret finns tillsammans rangordning svaret objekt inte innehåller den `resultIndex` fält.

## <a name="ranking-response-example"></a>Rangordning svar-exempel

Följande visar ett exempel [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Baserat på den här rangordning svar, visas sidopanelen två-enhetsresultat som rör Jimi Hendrix.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie för Entitetssökning i Bing](tutorial-bing-entities-search-single-page-app.md)
