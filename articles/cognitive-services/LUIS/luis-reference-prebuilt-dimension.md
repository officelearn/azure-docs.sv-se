---
title: Dimension Fördefinierade enheter - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller dimensionsfördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270637"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Dimensionsfördefinierade entitet för en LUIS-app
Den fördefinierade dimensionsentiteten identifierar olika typer av dimensioner, oavsett LUIS-appkulturen. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller dimensioner i programavsikterna. Dimensionentitet stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-dimension"></a>Typer av dimension

Dimensionen hanteras från [GitHub-databasen med identifierartext.](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)

## <a name="resolution-for-dimension-entity"></a>Lösning för dimensionsentitet

Följande entitetsobjekt returneras för frågan:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)
Följande JSON är `verbose` med parametern inställd `true`på:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
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

I följande exempel visas upplösningen för den **inbyggda** dimensionsentiteten.

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Läs mer om [e-post,](luis-reference-prebuilt-email.md) [nummer](luis-reference-prebuilt-number.md)och [ordningstal.](luis-reference-prebuilt-ordinal.md)
