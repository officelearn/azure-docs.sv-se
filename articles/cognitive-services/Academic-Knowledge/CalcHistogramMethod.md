---
title: CalcHistogram metoden – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Använda CalcHistogram-metoden för att beräkna fördelningen av attributvärden för en uppsättning entiteter för dokumentet.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: cb47b3933ecb6e38aa7945ac7f81f7602a0c8034
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192544"
---
# <a name="calchistogram-method"></a>CalcHistogram, metod

Den **calchistogram** REST-API används för att beräkna fördelningen av attributvärden för en uppsättning entiteter för dokumentet.          


**REST-slutpunkt:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>Parametrar för begäran

Name  |Value | Krävs?  |Beskrivning
-----------|----------|--------|----------
**uttryck för Markörstorlek**    |Textsträngen | Ja  |Ett frågeuttryck som anger de entiteter som att beräkna histogram.
**model** |Textsträngen | Nej |Välj namnet på den modell som du vill fråga.  För närvarande standardvärdet *senaste*.
**Attribut** | Textsträngen | Nej<br>standard: | En kommaavgränsad lista som anger de attributvärden som ingår i svaret. Attributnamn är skiftlägeskänsliga.
**antal** |Tal | Nej<br>Standard: 10 |Antalet resultat som ska returneras.
**offset**  |Tal | Nej<br>Standard: 0 |Index för det första resultatet ska returneras.
**timeout**  |Tal | Nej<br>Standard: 1000 |Tidsgräns i millisekunder. Endast tolkningar hittades före tidsgränsen har gått ut returneras.

## <a name="response-json"></a>Svar (JSON)

Name | Beskrivning
--------|---------
**uttryck för Markörstorlek**  |Parametern uttryck från begäran.
**num_entities** | Totalt antal matchande entiteter.
**Histogram** |  En matris med histogram, en för varje attribut som anges i begäran.
**Histogram [x] .attribute** | Namnet på attributet för vilken histogrammet beräknas.
**histograms[x].distinct_values** | Antal distinkta värden mellan matchande entiteter för det här attributet.
**histograms[x].total_count** | Totalt antal värdet instanser mellan matchande entiteter för det här attributet.
**Histogram [x] .histogram** | Histogramdata för det här attributet.
**histogram [.histogram [y] .value x]** |  Ett värde för attributet.
**histograms[x].histogram[y].logprob**  |Totalt antal naturliga loggen sannolikhet med matchande entiteter med det här attributvärdet.
**histograms[x].histogram[y].count**  |Antal matchande entiteter med det här attributvärdet.
**avbröts** | SANT om uppnåddes för begäran.


#### <a name="example"></a>Exempel:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>I det här exemplet för att generera ett histogram över antal publikationer per år för en viss författare sedan 2010 vi kan först skapa en fråga uttryck med hjälp av den **tolka** API med frågesträng: *papers av jaime teevan efter 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Uttrycket i den första tolkning som returneras från tolkningar API är *och (sammansatta (AA. AuN == ”jaime teevan”), Y > 2012)*.
<br>Det här uttrycksvärdet skickas sedan till den **calchistogram** API. Den *attributes=Y,F.FN* parametern anger att distributioner av dokumentet antal ska vara per år och fältet för studier, t.ex.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Svaret på den här begäran först indikerar att det finns 37 vitböcker som matchar frågeuttrycket.  För den *år* attribut, det finns 3 distinkta värden, en för varje år efter 2012 (d.v.s. 2013, 2014 och 2015) som anges i frågan.  Det totala antalet över 3 distinkta värden är 37.  För varje *år*, histogrammet visar värde, totalt antal naturliga loggen sannolikheten och antalet matchande entiteter.     

Histogram för *fält av studera* visar att det finns 34 distinkta fälten för undersökning. Som en dokumentet kan vara associerad med flera fält i studie, kan det totala antalet (53) vara större än antalet matchande entiteter.  Även om det finns 34 distinkta värden, svaret endast innehåller upp 4 grund av den *count = 4* parametern.

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
