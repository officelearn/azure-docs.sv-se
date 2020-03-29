---
title: Använda rankning för att visa svar - Bing Entity Search
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder rankning för att visa svaren som API:et för sökning av Bing-enhet returnerar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68423914"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Använda rankning för att visa entitetssökresultat  

Varje entitetssöksvar innehåller ett [RankResponse-svar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) som anger hur du måste visa sökresultat som returneras av API:et för sökning av Bing-entitet. Rankningssvarsgrupperna resulterar i pole, mainline och sidofältet innehåll. Pole resultatet är det viktigaste eller mest framträdande resultatet och bör visas först. Om du inte visar de återstående resultaten i ett traditionellt huvudlinje- och sidofältsformat måste du ge huvudlinjens innehåll högre synlighet än sidofältets innehåll. 
  
Inom varje grupp identifierar matrisen [Objekt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) den ordning som innehållet måste visas i. Varje objekt innehåller två sätt att identifiera resultatet i ett svar.  
 

|Field | Beskrivning  |
|---------|---------|
|`answerType` och `resultIndex` | `answerType`identifierar svaret (antingen entitet `resultIndex` eller Plats) och identifierar ett resultat i det svaret (till exempel en entitet). Indexet börjar vid 0.|
|`value`    | `value`Innehåller ett ID som matchar ID:t för antingen ett svar eller ett resultat i svaret. Antingen svaret eller resultaten innehåller ID men inte båda. |
  
Använda `answerType` och `resultIndex` är en tvåstegsprocess. Använd `answerType` först för att identifiera svaret som innehåller de resultat som ska visas. Använd `resultIndex` sedan för att indexera till det svarets resultat för att få resultatet att visas. (Värdet `answerType` är namnet på fältet i [SearchResponse-objektet.)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) Om du ska visa alla svarsresultat tillsammans innehåller svarsobjektet inte `resultIndex` fältet.

Om du använder ID:t måste du matcha rangordnings-ID:t med ID:t för ett svar eller något av dess resultat. Om ett svarsobjekt innehåller ett `id` fält visar du alla svarsresultat tillsammans. Om objektet `Entities` till exempel `id` innehåller fältet visar du alla entitetsartiklar tillsammans. Om `Entities` objektet inte innehåller `id` fältet innehåller varje entitet ett `id` fält och rangordningssvaret blandar entiteterna med resultaten Platser.  
  
## <a name="ranking-response-example"></a>Exempel på rankningssvar

Följande visar ett exempel [på RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Baserat på detta rankningssvar skulle sidofältet visa de två entitetsresultaten relaterade till Jimi Hendrix.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ensidig webbapp](tutorial-bing-entities-search-single-page-app.md)
