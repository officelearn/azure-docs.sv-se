---
title: Procentuell fördefinierad entitet - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller procentuell fördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270500"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procentuell fördefinierad entitet för en LUIS-app
Procentuella tal kan `3 1/2`visas som bråk, eller i `2%`procent. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller procent till programavsikterna. Procentigentitet stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Typer av procent
Procent hanteras från [GitHub-databasen med identifierartext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Lösning för fördefinierad procentigentitet

Följande entitetsobjekt returneras för frågan:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)
Följande JSON är `verbose` med parametern inställd `true`på:

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

I följande exempel visas upplösningen för den **inbyggda procententiteten.**

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [number](luis-reference-prebuilt-number.md) [ordnings-](luis-reference-prebuilt-ordinal.md) [och](luis-reference-prebuilt-temperature.md) temperatureniteter.
