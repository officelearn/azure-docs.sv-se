---
title: Entitetstyp för reguljära uttryck - LUIS
titleSuffix: Azure Cognitive Services
description: Ett reguljärt uttryck är bäst för rå yttrande text. Det ignorerar fallet och ignorerar kulturell variant.  Matchning av reguljära uttryck tillämpas efter ändringar av stavningskontroll på teckennivå, inte tokennivån.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841225"
---
# <a name="regular-expression-entity"></a>Entitet för reguljära uttryck

En entitet för reguljära uttryck extraherar en entitet baserat på ett mönster för reguljära uttryck som du anger.

Ett reguljärt uttryck är bäst för rå yttrande text. Det ignorerar fallet och ignorerar kulturell variant.  Matchning av reguljära uttryck tillämpas efter ändringar av stavningskontroll på teckennivå, inte tokennivån. Om det reguljära uttrycket är för komplext, till exempel med många hakparenteser, kan du inte lägga till uttrycket i modellen. Använder en del men inte hela [.NET Regex-biblioteket.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**Entiteten passar bra när:**

* Data är konsekvent formaterade med alla variationer som också är konsekventa.
* Det reguljära uttrycket behöver inte mer än 2 nivåer av kapsling.

![Entitet för reguljära uttryck](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Användningsöverväganden

Reguljära uttryck kan matcha mer än du förväntar dig att matcha. Ett exempel på detta är numerisk `one` `two`ordmatchning, till exempel och . Ett exempel är följande regex, `one` som matchar numret tillsammans med andra nummer:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Det här regex-uttrycket matchar också alla `phone`ord som slutar med dessa siffror, till exempel . För att åtgärda problem som denna, se till att regex matchar tar hänsyn till ordgränser. Regex för att använda ordgränser för det här exemplet används i följande regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Exempel på JSON

När `kb[0-9]{6}`du använder , som definitionen av reguljära uttryckentiteter, är följande JSON-svar ett exempel som uttryck med de returnerade reguljära uttrycksentiteterna för frågan:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)


Detta är JSON `verbose=false` om anges i frågesträngen:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Detta är JSON `verbose=true` om anges i frågesträngen:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Nästa steg

Skapa en app i den här [självstudien](tutorial-regex-entity.md)för att extrahera konsekvent formaterade data från ett uttryck med hjälp av entiteten **Reguljärt uttryck.**