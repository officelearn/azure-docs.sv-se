---
title: Analysera metod - API för Lingvistisk analys
titlesuffix: Azure Cognitive Services
description: Hur du använder analysera-metod i API för Lingvistisk analys för att analysera vissa naturliga språkinmatningar.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ms.openlocfilehash: c8d380a23c1bbfca8258ef533453050c72a3abd0
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129632"
---
# <a name="analyze-method"></a>Analysera metod

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

Namn | Typ | Krävs | Beskrivning
-----|-------|----------|------------
**Språk**    | sträng | Ja | De två enhetsbokstaven ISO språkkoden som ska användas för analys. Engelska är till exempel ”SV”.
**analyzerIds** | lista med strängar | Ja | Lista över GUID-numren för analysverktyg tillämpas. Analysverktyg dokumentationen för mer information.
**Text**        | sträng | Ja | Rå-indata som ska analyseras. Det kan vara en kort sträng, till exempel ett ord eller fraser, en fullständig mening eller en fullständig punkt eller discourse.

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
resultatet [x]. Förskjutning | int | Starta en teckenförskjutning av varje mening |
resultatet [x]. Len | int | Längden i tecken för varje mening |
resultatet [x]. Token | lista över token-objekt | token gränser som anges i meningen |
resultatet [x]. Token [y]. Förskjutning | int | Starta en teckenförskjutning av token |
resultatet [x]. Token [y]. Len | int | Längden i tecken för token |
resultatet [x]. Token [y]. RawToken | sträng | tecknen inom denna token innan normalisering |
resultatet [x]. Token [y]. NormalizedToken | sträng | en normaliserad form av tecknet säker för användning i en [parsa trädet](Constituency-Parsing.md); exempelvis ett öppna parentesen tecken ' (' blir - LRB - |

Exempel på indata ”: det här är ett test. Hello ”.
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


### <a name="pos-tags-response-json"></a>POS-taggar svar (JSON)

Resultatet är lista med listor med strängar.
Det finns en lista över POS-taggar en POS-tagg för varje token för varje mening.
För att hitta den token som motsvarar varje POS-tagg kan du be om ett tokenisering-objekt.

### <a name="constituency-tree-response-json"></a>Av valkrets trädet svar (JSON)

Resultatet är en lista med strängar, en parsningsträd för varje mening listvärdena i indata.
Parsa träd representeras i form av en du.

## <a name="example"></a>Exempel

`POST /analyze`

Begärandetexten: JSON-nyttolast
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

