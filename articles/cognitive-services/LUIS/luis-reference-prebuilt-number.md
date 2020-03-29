---
title: Antal fördefinierade entitet - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information om numrerar fördefinierad entitet i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273469"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Numrera fördefinierad entitet för en LUIS-app
Det finns många sätt på vilka numeriska värden används för att kvantifiera, uttrycka och beskriva informationsdelar. Den här artikeln innehåller bara några av de möjliga exemplen. LUIS tolkar variationerna i användaryttranden och returnerar konsekventa numeriska värden. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller nummer i programavsikterna.

## <a name="types-of-number"></a>Typer av nummer
Numret hanteras från [GitHub-databasen för identifierartext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Exempel på talupplösning

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


LUIS innehåller det redovisade värdet för en entitet **`builtin.number`** i `resolution` fältet för det JSON-svar som returneras.

## <a name="resolution-for-prebuilt-number"></a>Upplösning för fördefinierat tal

Följande entitetsobjekt returneras för frågan:

`order two dozen eggs`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)

Följande JSON är `verbose` med parametern inställd `true`på:

```json
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
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-svar](#tab/V2)

Följande exempel visar ett JSON-svar från LUIS, som innehåller upplösningen av värdet 24, för uttrycket "två dussin".

```json
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
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [valutan,](luis-reference-prebuilt-currency.md) [ordningstalen](luis-reference-prebuilt-ordinal.md)och [procenttalet](luis-reference-prebuilt-percentage.md).
