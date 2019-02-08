---
title: Analysera metod - API för Lingvistisk analys
titlesuffix: Azure Cognitive Services
description: Hur du använder analysera-metod i API för Lingvistisk analys för att analysera vissa naturliga språkinmatningar.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 2b02b048719dd7707db7e97df3641a314b512177
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861688"
---
# <a name="analyze-method"></a>Analysera metod

> [!IMPORTANT]
> Förhandsversionen av Språkanalys upphörde den 9 augusti 2018. Vi rekommenderar att du använder [Azure Machine Learning-textanalysmoduler](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) för textbearbetning och -analys.

Den **analysera** REST-API används för att analysera en viss naturligt språk som indata.
Som kan innebära att bara söka efter den [meningar och token](Sentences-and-Tokens.md) inom som indata, att hitta den [av ordklasser taggar](POS-tagging.md), eller för att hitta den [constitutency trädet](Constituency-Parsing.md).
Du kan ange vilka resultat som du vill ha genom att välja relevanta analysverktyg.
Om du vill visa alla tillgängliga analysverktyg, titta på den  **[analysverktyg](AnalyzersMethod.md)**.

Observera att du måste ange språket för den inmatade strängen.

**REST-slutpunkt:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Begäranparametrar

Namn | Type | Obligatoriskt | Beskrivning
-----|-------|----------|------------
**language**    | sträng | Ja | De två enhetsbokstaven ISO språkkoden som ska användas för analys. Engelska är till exempel ”SV”.
**analyzerIds** | lista med strängar | Ja | Lista över GUID-numren för analysverktyg tillämpas. Analysverktyg dokumentationen för mer information.
**text**        | sträng | Ja | Rå-indata som ska analyseras. Det kan vara en kort sträng, till exempel ett ord eller fraser, en fullständig mening eller en fullständig punkt eller discourse.

## <a name="response-json"></a>Svar (JSON)

En matris med analys av utdata, en för varje attribut som anges i begäran.

Resultaten se ut så här:

Namn | Typ | Beskrivning
-----|------|--------------
analyzerId | sträng | GUID för analysatorn anges
Resultatet | objekt | Analyzer-resultat

Observera att typ av resultatet beror på vilken typ av inkommande analyzer.

### <a name="tokens-response-json"></a>Token-svar (JSON)

Namn | Typ | Beskrivning
-----|------|-------------
Resultatet | lista över mening objekt | mening gränser som anges i texten |
result[x].Offset | int | Starta en teckenförskjutning av varje mening |
result[x].Len | int | Längden i tecken för varje mening |
result[x].Tokens | lista över token-objekt | token gränser som anges i meningen |
result[x].Tokens[y].Offset | int | Starta en teckenförskjutning av token |
result[x].Tokens[y].Len | int | Längden i tecken för token |
result[x].Tokens[y].RawToken | sträng | tecknen inom denna token innan normalisering |
resultatet [x]. Token [y]. NormalizedToken | sträng | en normaliserad form av tecknet säker för användning i en [parsa trädet](Constituency-Parsing.md); exempelvis ett öppna parentesen tecken ' (' blir - LRB - |

Exempel på indata ”: det här är ett test. Hello.'
Exempel-JSON-svar:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>POS Tags Response (JSON)

Resultatet är lista med listor med strängar.
Det finns en lista över POS-taggar en POS-tagg för varje token för varje mening.
För att hitta den token som motsvarar varje POS-tagg kan du be om ett tokenisering-objekt.

### <a name="constituency-tree-response-json"></a>Av valkrets trädet svar (JSON)

Resultatet är en lista med strängar, en parsningsträd för varje mening listvärdena i indata.
Parsa träd representeras i form av en du.

## <a name="example"></a>Exempel

`POST /analyze`

Brödtext i begäran: JSON-nyttolast
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

Svar: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
