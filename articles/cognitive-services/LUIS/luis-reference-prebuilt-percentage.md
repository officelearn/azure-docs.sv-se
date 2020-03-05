---
title: Fördefinierad enhet i procent – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller procent fördefinierade entitetsinformation i Språkförståelse (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270500"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Fördefinierad procent andel av en LUIS-app
Procent andelar kan visas som bråktal, `3 1/2`eller i procent `2%`. Eftersom den här entiteten har redan tränats, behöver du inte att lägga till exempel yttranden som innehåller andel till programmet avsikter. Enheten i procent stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Typer av procent
Procent andelen hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Lösning för fördefinierade procent entitet

Följande enhets objekt returneras för frågan:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är med parametern `verbose` som har angetts till `false`:

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON är med parametern `verbose` som har angetts till `true`:

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

I följande exempel visas en lösning på enheten **Builtin. procent** .

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

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om entiteterna [ordning](luis-reference-prebuilt-ordinal.md), [antal](luis-reference-prebuilt-number.md)och [temperatur](luis-reference-prebuilt-temperature.md) .
