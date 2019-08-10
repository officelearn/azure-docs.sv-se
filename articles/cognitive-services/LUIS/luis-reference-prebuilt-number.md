---
title: Fördefinierad entitet för nummer – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller ett antal fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f2868610bf9215b1414ea3ba342e3064b9c54f63
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933509"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Numrerad fördefinierad entitet för en LUIS-app
Det finns många sätt som numeriska värden som används för att kvantifiera, express och beskriver typer av information. Den här artikeln beskriver bara några av exempel som möjligt. LUIS tolkar variationerna i användaren uttryck och returnerar konsekvent numeriska värden. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller tal som ska programmet avsikter. 

## <a name="types-of-number"></a>Typer av nummer
Antalet hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Exempel på antalet upplösning

| Yttrande        | Entitet   | Lösning |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS innehåller värdet som känns igen av en **`builtin.number`** entitet i den `resolution` i JSON-svar returneras.

## <a name="resolution-for-prebuilt-number"></a>Lösning för fördefinierade nummer


### <a name="api-version-2x"></a>API-version 2. x

I följande exempel visar ett JSON-svar från LUIS, som innehåller av lösningen på värdet 24, för uttryck ”24”.

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Förhandsgranska API version 3. x

Följande JSON- `verbose` parameter har angetts till `false`:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ]
        }
    }
}
```

Följande JSON- `verbose` parameter har angetts till `true`:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "two dozen",
                        "startIndex": 6,
                        "length": 9,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [valuta](luis-reference-prebuilt-currency.md), [ordningstal](luis-reference-prebuilt-ordinal.md), och [procent](luis-reference-prebuilt-percentage.md). 
