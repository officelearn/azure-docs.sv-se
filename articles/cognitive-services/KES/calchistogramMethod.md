---
title: CalcHistogram metod - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder-metoden CalcHistogram i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: f994a254e661cd245d2e953efd3bd595d50c6fc7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214321"
---
# <a name="calchistogram-method"></a>calchistogram metod
Den *calchistogram* metoden beräknar de objekt som matchar ett strukturerade frågeuttryck och beräknar fördelningen av deras attributvärden.

## <a name="request"></a>Begäran
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name|Value|Beskrivning
----|-----|-----------
uttryck för Markörstorlek | Textsträngen | Strukturerade frågeuttryck som anger index-entiteter över som du vill beräkna histogram.
Attribut | Textsträng (standard = ””) | Kommaavgränsad lista över attribut som ska inkluderas i svaret.
 antal   | Antal (standard = 10) | Antalet resultat som ska returneras.
offset  | Antal (standard = 0) | Index för det första resultatet ska returneras.

## <a name="response-json"></a>Svar (JSON)
JSONPath | Beskrivning
----|----
$.expr | *uttryck* parametern från begäran.
$.num_entities | Totalt antal matchande entiteter.
$.histograms |  Matris med histogram, en för varje begärda attribut.
$.histograms[\*].attribute | Namnet på attributet för vilken histogrammet beräknas.
$.histograms[\*].distinct_values | Antal distinkta värden mellan matchande entiteter för det här attributet.
$.histograms[\*].total_count | Totalt antal värdet instanser mellan matchande entiteter för det här attributet.
$.histograms[\*].histogram | Histogramdata för det här attributet.
$.histograms[\*].histogram[\*].value | Attributvärdet.
$.histograms[\*].histogram[\*].logprob  | Totalt antal naturliga loggen sannolikhet med matchande entiteter med det här attributvärdet.
$.histograms[\*].histogram[\*].count    | Antal matchande entiteter med det här attributvärdet.
$.aborted | SANT om uppnåddes för begäran.

### <a name="example"></a>Exempel
I exemplet akademiska publikationer beräknar följande ett histogram för publikationen antal per år och efter nyckelord för en viss författare sedan 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

Svaret anger att det finns 37 papers matchar frågeuttrycket.  För den *år* attribut, det finns 3 distinkta värden, en för varje år sedan 2013.  Det totala antalet över 3 distinkta värden är 37.  För varje *år*, histogrammet visar värde, totalt antal naturliga loggen sannolikheten och antalet matchande entiteter.     

Histogram för *nyckelordet* visar att det finns 34 distinkta nyckelord. När ett dokument kan vara associerad med flera nyckelord, kan det totala antalet (53) vara större än antalet matchande entiteter.  Även om det finns 34 distinkta värden, svaret endast innehåller upp 4 grund av den ”antal = 4” parametern.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
