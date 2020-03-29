---
title: Temperatur fördefinierad enhet - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information om fördefinierad entitet för temperatur i SPRÅKFÖRSTÅELSE (LUIS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270373"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Fördefinierad entitet för temperatur för en LUIS-app
Temperatur extraherar en mängd olika temperaturtyper. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller temperatur i programmet. Temperaturentitet stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-temperature"></a>Typer av temperatur
Temperaturen hanteras från [GitHub-databasen med identifierartext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Upplösning för fördefinierad temperaturenhet

Följande entitetsobjekt returneras för frågan:

`set the temperature to 30 degrees`


#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

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
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)
Följande JSON är `verbose` med parametern inställd `true`på:

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

I följande exempel visas upplösningen för den **inbyggda.temperatureniteten.**

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

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Läs mer om [procent-](luis-reference-prebuilt-percentage.md) [och](luis-reference-prebuilt-number.md)åldersentiteter. [age](luis-reference-prebuilt-age.md)
