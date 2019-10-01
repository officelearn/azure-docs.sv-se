---
title: Enhets typ för reguljärt uttryck – LUIS
titleSuffix: Azure Cognitive Services
description: Ett reguljärt uttryck passar bäst för rå uttryck text. Det är inte skiftlägeskänslig och ignorerar kulturella variant.  Matchning med reguljära uttryck tillämpas efter ändringar av stavningskontroll på teckennivån, inte på token-nivå.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: ae46df875d588186cd083134820f349158d7e307
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695207"
---
# <a name="regular-expression-entity"></a>Entitet för reguljära uttryck 

En entitet för reguljära uttryck extraherar en entitet baserat på ett mönster för reguljära uttryck som du anger.

Ett reguljärt uttryck passar bäst för rå uttryck text. Det är inte skiftlägeskänslig och ignorerar kulturella variant.  Matchning med reguljära uttryck tillämpas efter ändringar av stavningskontroll på teckennivån, inte på token-nivå. Om det reguljära uttrycket är för komplex, t. ex. genom att använda många hakparenteser, kan du inte lägga till uttrycket i modellen. Använder en del men inte alla [.net regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) -bibliotek. 

**Entiteten passar bra när:**

* Data formateras konsekvent med alla variationer som också är konsekventa.
* Det reguljära uttrycket behöver inte fler än 2 kapslings nivåer. 

![Entitet för reguljära uttryck](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Användnings överväganden

Reguljära uttryck kan matcha mer än vad du förväntar dig att matcha. Ett exempel på detta är numerisk ord matchning, till exempel `one` och `two`. Ett exempel är följande regex, som matchar antalet `one` tillsammans med andra tal:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Det här regex-uttrycket matchar även ord som slutar med talen, till exempel `phone`. För att åtgärda problem som detta måste du kontrol lera att regex-matchningarna tar hänsyn till ord gränser. Regex för att använda ord gränser för det här exemplet används i följande regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Exempel på JSON

När du använder `kb[0-9]{6}`, som definitionen av entiteten för reguljära uttryck, är följande JSON-svar ett exempel på uttryck med returnerade reguljära uttrycks enheter för frågan:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)


Detta är JSON om `verbose=false` anges i frågesträngen:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Detta är JSON om `verbose=true` anges i frågesträngen:

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

I den här [självstudien](luis-quickstart-intents-regex-entity.md)skapar du en app för att extrahera konsekvent formaterad data från en uttryck med hjälp av entiteten för **reguljära uttryck** .