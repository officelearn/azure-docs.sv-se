---
title: CalcHistogram-metoden i Academic Knowledge API | Microsoft Docs
description: Använd metoden CalcHistogram för att beräkna fördelningen av attributvärden för en uppsättning papper enheter i Microsoft kognitiva tjänster.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: e0b773fb9791ee638c8cfdbbc9dca40543e50ec0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351426"
---
# <a name="calchistogram-method"></a>CalcHistogram-metoden

Den **calchistogram** REST API för att beräkna fördelningen av attributvärden för en uppsättning papper entiteter.          


**REST-slutpunkt:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>Begäranparametrar

Namn  |Värde | Krävs?  |Beskrivning
-----------|----------|--------|----------
**uttryck**    |Textsträng | Ja  |Ett frågeuttryck som anger de enheter som ska beräkna histogram över.
**modellen** |Textsträng | Nej |Välj namnet på den modell som du vill fråga.  För närvarande standardvärdet *senaste*.
**attribut** | Textsträng | Nej<br>standard: | En kommaavgränsad lista som anger de attributvärden som ingår i svaret. Attributnamn är skiftlägeskänsliga.
**Antal** |Tal | Nej<br>Standard: 10 |Antalet resultat som ska returneras.
**förskjutning**  |Tal | Nej<br>Standard: 0 |Indexet för det första resultatet ska returneras.
<br>
## <a name="response-json"></a>Svar (JSON)
Namn | Beskrivning
--------|---------
**uttryck**  |Parametern uttryck i begäran.
**num_entities** | Totalt antal matchande entiteter.
**Histogram** |  En matris med histogram, ett för varje attribut som anges i begäran.
**Histogram [x] .attribute** | Namnet på attributet som histogrammet beräknades.
**Histogram [x] .distinct_values** | Antalet distinkta värden mellan matchar entiteter för det här attributet.
**Histogram [x] .total_count** | Totalt antal instanser av värdet mellan matchar entiteter för det här attributet.
**Histogram [x] .histogram** | Histogramdata för det här attributet.
**histogram [.value .histogram [-y] x]** |  Ett värde för attributet.
**histogram [.logprob .histogram [-y] x]**  |Totalt antal den naturliga logaritmen sannolikheten för matchar entiteter med det här attributvärdet.
**histogram [.count .histogram [-y] x]**  |Antal matchande entiteter med det här attributvärdet.
**avbröts** | TRUE om tidsgränsen för begäran.

 <br>
#### <a name="example"></a>Exempel:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>I det här exemplet för att generera ett histogram antal publikationer per år för en viss författare sedan 2010 vi kan först generera frågan uttryck med den **tolka** API med frågesträng: *papers av jaime teevan efter 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Uttrycket i första tolkning som returneras från interpret API är *och (sammansatt (AA. AuN == ”jaime teevan”), Y > 2012)*.
<br>Det här uttrycksvärdet skickas sedan till den **calchistogram** API. Den *attributes=Y,F.FN* parametern anger att distributioner av dokumentet antal ska vara per år och fältet för undersökning, t.ex.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Svaret på begäran först betyder det att är 37 dokument som matchar frågeuttrycket.  För den *år* attribut, 3 distinkta värden, en för varje år efter 2012 (d.v.s. 2013 2014 och 2015) som angetts i frågan.  Det totala antalet över 3 distinkta värden är 37.  För varje *år*, histogrammet visar värdet, totalt antal den naturliga logaritmen sannolikhet och räkna matchande entiteter.     

Histogram för *fält av studera* visar att det finns 34 olika fält för undersökning. Som en papper kan vara kopplad till flera fält för undersökning, kan det totala antalet (53) vara större än antalet matchande entiteter.  Även om det finns 34 distinkta värden svaret endast innehåller upp 4 eftersom den *count = 4* parameter.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
