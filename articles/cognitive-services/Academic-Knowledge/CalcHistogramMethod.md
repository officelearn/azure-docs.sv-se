---
title: CalcHistogram-metod – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Använd metoden CalcHistogram för att beräkna fördelningen av attributvärden för en uppsättning pappers enheter.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 9e84b1ad37b3224ec5553d0a66ba0fc84bc88f55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705068"
---
# <a name="calchistogram-method"></a>CalcHistogram-metod

**Calchistogram** -REST API används för att beräkna fördelningen av attributvärden för en uppsättning pappers enheter.          


**REST-slutpunkt:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>Begäranparametrar

Namn  |Värde | Obligatorisk?  |Beskrivning
-----------|----------|--------|----------
**uttrycks**    |Text sträng | Ja  |Ett frågeuttryck som anger de entiteter som histogram ska beräknas för.
**förlag** |Text sträng | Nej |Välj namnet på den modell som du vill fråga.  För närvarande är standardvärdet *senaste*.
**dokumentattribut** | Text sträng | Nej<br>objekt | En kommaavgränsad lista som anger de attributvärden som ingår i svaret. Attributnamn är Skift läges känsliga.
**antal** |Number | Nej<br>Standard: 10 |Antal resultat som ska returneras.
**redovisningsmotkonto**  |Number | Nej<br>Standard: 0 |Index för det första resultat som ska returneras.
**standardvärde**  |Number | Nej<br>Standard: 1000 |Timeout i millisekunder. Endast tolkningar som hittas innan tids gränsen har förflutit returneras.

## <a name="response-json"></a>Svar (JSON)

Namn | Beskrivning
--------|---------
**uttrycks**  |Expr-parametern från begäran.
**num_entities** | Totalt antal matchande entiteter.
**histogram** |  En matris med histogram, ett för varje attribut som anges i begäran.
**histogram [x]. attribut** | Namnet på attributet som histogrammet beräknades för.
**histograms[x].distinct_values** | Antalet distinkta värden bland matchande entiteter för det här attributet.
**histograms[x].total_count** | Totalt antal värde instanser mellan matchande entiteter för det här attributet.
**histogram [x]. histogram** | Histogram data för det här attributet.
**histogram [x]. histogram [y]. värde** |  Ett värde för attributet.
**histogram [x]. histogram [y]. logprob**  |Total sannolikhet för naturlig logg för matchande entiteter med det här attributvärdet.
**histogram [x]. histogram [y]. Count**  |Antal matchande entiteter med det här attributvärdet.
**avbröts** | Sant om tids gränsen för begäran uppnåddes.


#### <a name="example"></a>Exempel:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>I det här exemplet, för att generera ett histogram över antalet publikationer per år för en viss författare sedan 2010, kan vi först generera frågeuttrycket med hjälp **av API:** et with frågesträng: *Papers by kolla teevan efter 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Uttrycket i den första tolkningen som returneras från tolknings-API: et är *och (sammansatt (AA). AuN = = ' kolla teevan '), Y > 2012)* .
<br>Det här uttrycks värdet skickas sedan till **calchistogram** -API: et. Parametern *attributes = Y, F. FN* anger att distributioner av pappers antal ska vara per år och studie analys, t. ex.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Svaret på denna begäran anger först att det finns 37-blad som matchar frågeuttrycket.  För attributet *Year* finns det tre distinkta värden, ett för varje år efter 2012 (t. ex. 2013, 2014 och 2015) som anges i frågan.  Det totala antalet papper över de tre distinkta värdena är 37.  I varje *år*visar histogrammet värdet, den totala sannolikheten för naturlig logg och antalet matchande entiteter.     

Histogrammet för *ämnes fältet* visar att det finns 34 distinkta analys fält. Som ett papper kan vara kopplat till flera analys fält, det totala antalet (53) kan vara större än antalet matchande entiteter.  Även om det finns 34 distinkta värden, innehåller svaret bara de fyra främsta på grund av parametern *Count = 4* .

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
