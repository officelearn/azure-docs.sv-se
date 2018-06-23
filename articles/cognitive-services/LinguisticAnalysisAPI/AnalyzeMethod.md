---
title: Analysera metod i språkliga analys API | Microsoft Docs
description: Hur du använder metoden analysera i språkliga analys API för att analysera vissa naturligt språk indata.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 12/13/2016
ms.author: lesun
ms.openlocfilehash: b17a00f31845bfa05572dff7ca94e9a1ffd69586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351735"
---
# <a name="analyze-method"></a>Analysera metod

Den **analysera** REST-API används för att analysera ett visst naturligt språk indata.
Som kan handla om att bara söka efter den [meningar och token](Sentences-and-Tokens.md) inom som indata, söka efter den [en del av tal taggar](POS-tagging.md), eller för att hitta den [constitutency trädet](Constituency-Parsing.md).
Du kan ange vilka resultat som du vill ha genom att välja relevanta analyzers.
Om du vill visa en lista över alla tillgängliga analyzers, titta på den  **[analyzers](AnalyzersMethod.md)**.

Observera att du måste ange språk för den inmatade strängen.

**REST-slutpunkt:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Begäranparametrar

Namn | Typ | Krävs | Beskrivning
-----|-------|----------|------------
**Språk**    | sträng | Ja | De två enhetsbokstaven ISO språkkod som ska användas för analys. Engelska är till exempel ”SV”.
**analyzerIds** | lista med strängar | Ja | Lista över GUID för analyzers ska gälla. Analyzers dokumentationen för mer information.
**text**        | sträng | Ja | Rådata indata som ska analyseras. Det kan vara en kort sträng, till exempel ett ord eller fraser, en fullständig mening eller en fullständig punkt eller discourse.

<br>
## <a name="response-json"></a>Svar (JSON)
En matris med analys utdata, en för varje attribut som anges i begäran.

Resultatet ser ut så här:

Namn | Typ | Beskrivning
-----|------|--------------
analyzerId | sträng | GUID för analyzer angetts
resultat | objekt | Analyzer resultat

Observera att typ av resultatet beror på vilken typ av inkommande analyzer.

### <a name="tokens-response-json"></a>Token svar (JSON)

Namn | Typ | Beskrivning
-----|------|-------------
resultat | lista över meningen objekt | meningen gränser som identifieras i texten |
resultatet [x]. Förskjutning | int | första teckenförskjutningen i varje mening |
resultatet [x]. Len | int | Längden i tecken för varje mening |
resultatet [x]. Token | lista över token objekt | token gränser som definierats i meningen |
resultatet [x]. Token [-y]. Förskjutning | int | första teckenförskjutningen av token |
resultatet [x]. Token [-y]. Len | int | Längden i tecken för token |
resultatet [x]. Token [-y]. RawToken | sträng | tecknen inom denna token innan normalisering |
resultatet [x]. Token [-y]. NormalizedToken | sträng | normaliserade form av tecknet som säkra för användning i en [parsa trädet](Constituency-Parsing.md); exempelvis ett Vänsterparentestecken ' (' blir - LRB - |

Exempel indata ”: det här är ett test. Hello ”.
Exempel JSON-svar:
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


### <a name="pos-tags-response-json"></a>POS taggar svar (JSON)

Resultatet är en lista med listor med strängar.
För varje mening finns en lista över POS taggar en POS-tagg för varje token.
Om du vill hitta det token som motsvarar varje POS-tagg, ska du be om ett tokenisering-objekt.

### <a name="constituency-tree-response-json"></a>Område trädet svar (JSON)

Resultatet är en lista med strängar, en parse-träd för varje mening listvärdena i indata.
Parse-träd representeras i ett formulär (inom parentes).

<br>

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

Svaret: JSON
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

