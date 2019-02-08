---
title: Med hjälp av rangordning för att visa svar - Entitetssökning i Bing
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder rangordning för att visa de svar som returnerar de Entitetssökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9e2a4075436145a0cc185b7ab1b406fa8d27b8e3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867842"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Med hjälp av rangordning för att visa resultat från  

Varje entitet search svaret innehåller en [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) svar som anger hur du måste visa sökresultat som returneras av den Entitetssökning i Bing. Rangordning svaret grupperar resultaten till pol, likriktade, och sidopanelen innehåll. Pol resultatet är mest viktigt eller framträdande resultatet och ska visas först. Om du inte vill visa de återstående resulterar i en traditionell likriktade och sidopanelen format, måste du ange likriktade innehåll högre synlighet än sidopanelen innehållet. 
  
Inom varje grupp i [objekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) matris identifierar den ordning som innehållet måste finnas i. Varje objekt innehåller två metoder för att identifiera resultatet i ett svar.  
 

|Fält | Beskrivning  |
|---------|---------|
|`answerType` och `resultIndex` | `answerType` identifierar svar (entitet eller plats) och `resultIndex` identifierar ett resultat i svaret (till exempel en entitet). Index som börjar vid 0.|
|`value`    | `value` Innehåller ett ID som matchar ID: T för ett svar eller ett resultat i svaret. Svaret eller resultatet innehålla ID men inte båda. |
  
Med hjälp av den `answerType` och `resultIndex` är en tvåstegsprocess. Använd först `answerType` att identifiera det svar som innehåller resultatet ska visas. Använd sedan `resultIndex` till index till den svar resultat att få resultat att visa. (Den `answerType` värdet är namnet på fältet i den [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objekt.) Om du ska visa resultat som svaret finns tillsammans rangordning svaret objekt inte innehåller den `resultIndex` fält.

Med hjälp av ID måste du matcha rangordning ID med ID: T för ett svar eller ett av resultaten. Om ett svar-objekt innehåller en `id` fältet, visa resultat som svaret finns tillsammans. Till exempel om den `Entities` objektet innehåller de `id` fältet, visa alla entiteter artiklar tillsammans. Om den `Entities` objekt omfattar inte den `id` fältet och sedan varje entitet innehåller en `id` fältet och svaret rangordning blandas entiteterna med platser resultaten.  
  
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
> [Skapa en enkelsidig webbapp](tutorial-bing-entities-search-single-page-app.md)
