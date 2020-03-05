---
title: Fördefinierad temperatur enhet – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller temperatur fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 7e2b48c6353f56ab2269a8718146cb765797adba
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270373"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Fördefinierad temperatur enhet för en LUIS-app
Temperatur extraherar en mängd olika typer av temperatur. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller temperatur till programmet. Temperatur enheten stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>Typer av temperatur
Temperaturen hanteras från [Recognizer-text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) lagringsplatsen

## <a name="resolution-for-prebuilt-temperature-entity"></a>Lösning för fördefinierade temperatur entitet

Följande enhets objekt returneras för frågan:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är med parametern `verbose` som har angetts till `false`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON är med parametern `verbose` som har angetts till `true`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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

I följande exempel visas upplösningen för enheten **Builtin. temperatur** .

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om enheterna [procent](luis-reference-prebuilt-percentage.md), [antal](luis-reference-prebuilt-number.md)och [ålder](luis-reference-prebuilt-age.md) .
