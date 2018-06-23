---
title: CalcHistogram-metoden i Knowledge utforskning Service API | Microsoft Docs
description: Lär dig hur du använder metoden CalcHistogram i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 6ed694b0cc9cf41b815cc54b9f6d12adb2b7cd64
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351624"
---
# <a name="calchistogram-method"></a>calchistogram metod
Den *calchistogram* metoden beräknar objekt som matchar ett strukturerade frågeuttryck och beräknar fördelningen av deras attributvärden.

## <a name="request"></a>Förfrågan
`http://<host>/calchistogram?expr=<expr>[&options]` 

Namn|Värde|Beskrivning
----|-----|-----------
uttryck | Textsträng | Strukturerade frågeuttryck som anger entiteterna index över som du vill beräkna histogram.
attribut | Sträng (standard = ””) | Kommaavgränsad lista över attribut som ska inkluderas i svaret.
count   | Antal (standard = 10) | Antalet resultat som ska returneras.
förskjutning  | Antal (standard = 0) | Indexet för det första resultatet ska returneras.

## <a name="response-json"></a>Svar (JSON)
JSONPath | Beskrivning
----|----
$.expr | *uttryck* parametern från begäran.
$.num_entities | Totalt antal matchande entiteter.
$.histograms |  Matris med histogram, ett för varje begärda attribut.
$.histograms [\*] .attribute | Namnet på attributet som histogrammet beräknades.
$.histograms [\*] .distinct_values | Antalet distinkta värden mellan matchar entiteter för det här attributet.
$.histograms [\*] .total_count | Totalt antal instanser av värdet mellan matchar entiteter för det här attributet.
$.histograms [\*] .histogram | Histogramdata för det här attributet.
$.histograms [\*] .histogram [\*] .value | Attributvärdet.
$.histograms [\*] .histogram [\*] .logprob  | Totalt antal den naturliga logaritmen sannolikheten för matchar entiteter med det här attributvärdet.
$.histograms [\*] .histogram [\*] .count    | Antal matchande entiteter med det här attributvärdet.
$.aborted | TRUE om tidsgränsen för begäran.

### <a name="example"></a>Exempel
I exemplet academic publikationer beräknar följande ett histogram för publikationen antal per år och nyckelord för en viss författare sedan 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

Svaret anger att det finns 37 papper matchar frågeuttrycket.  För den *år* attribut, 3 distinkta värden, en för varje år sedan 2013.  Det totala antalet över 3 distinkta värden är 37.  För varje *år*, histogrammet visar värdet, totalt antal den naturliga logaritmen sannolikhet och räkna matchande entiteter.     

Histogram för *nyckelordet* visar att det finns 34 distinkta nyckelord. Som en papper kan vara kopplad till flera nyckelord, kan det totala antalet (53) vara större än antalet matchande entiteter.  Även om det finns 34 distinkta värden svaret endast innehåller upp 4 eftersom den ”count = 4” parametern.

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
