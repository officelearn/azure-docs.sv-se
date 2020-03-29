---
title: Geografi V2 fördefinierad enhet - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller geografiV2 fördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: b2b2b0781abce59628660b669f43110bf91b15e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273498"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>GeografiV2 fördefinierad entitet för en LUIS-app
Den fördefinierade geografiV2 entiteten identifierar platser. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller GeographyV2 i programavsikterna. GeographyV2 enhet stöds i engelsk [kultur](luis-reference-prebuilt-entities.md).

## <a name="subtypes"></a>Undertyper
De geografiska platserna har undertyper:

|Subtyp|Syfte|
|--|--|
|`poi`|intressepunkt|
|`city`|namn på staden|
|`countryRegion`|namn på land eller region|
|`continent`|namnet på kontinenten|
|`state`|namn på stat eller provins|


## <a name="resolution-for-geographyv2-entity"></a>Lösning för geographyV2-entitet

Följande entitetsobjekt returneras för frågan:

`Carol is visiting the sphinx in gizah egypt in africa before heading to texas.`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "geographyV2": [
        {
            "value": "the sphinx",
            "type": "poi"
        },
        {
            "value": "gizah",
            "type": "city"
        },
        {
            "value": "egypt",
            "type": "countryRegion"
        },
        {
            "value": "africa",
            "type": "continent"
        },
        {
            "value": "texas",
            "type": "state"
        }
    ]
}
```

I föregående JSON, `poi` är en förkortning för **Point of Interest**.

#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)

Följande JSON är `verbose` med parametern inställd `true`på:

```json
"entities": {
    "geographyV2": [
        {
            "value": "the sphinx",
            "type": "poi"
        },
        {
            "value": "gizah",
            "type": "city"
        },
        {
            "value": "egypt",
            "type": "countryRegion"
        },
        {
            "value": "africa",
            "type": "continent"
        },
        {
            "value": "texas",
            "type": "state"
        }
    ],
    "$instance": {
        "geographyV2": [
            {
                "type": "builtin.geographyV2.poi",
                "text": "the sphinx",
                "startIndex": 18,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.city",
                "text": "gizah",
                "startIndex": 32,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.countryRegion",
                "text": "egypt",
                "startIndex": 38,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.continent",
                "text": "africa",
                "startIndex": 47,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.geographyV2.state",
                "text": "texas",
                "startIndex": 72,
                "length": 5,
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

I följande exempel visas upplösningen för den **builtin.geographyV2-entiteten.**

```json
"entities": [
    {
        "entity": "the sphinx",
        "type": "builtin.geographyV2.poi",
        "startIndex": 18,
        "endIndex": 27
    },
    {
        "entity": "gizah",
        "type": "builtin.geographyV2.city",
        "startIndex": 32,
        "endIndex": 36
    },
    {
        "entity": "egypt",
        "type": "builtin.geographyV2.countryRegion",
        "startIndex": 38,
        "endIndex": 42
    },
    {
        "entity": "africa",
        "type": "builtin.geographyV2.continent",
        "startIndex": 47,
        "endIndex": 52
    },
    {
        "entity": "texas",
        "type": "builtin.geographyV2.state",
        "startIndex": 72,
        "endIndex": 76
    },
    {
        "entity": "carol",
        "type": "builtin.personName",
        "startIndex": 0,
        "endIndex": 4
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Läs mer om [e-post,](luis-reference-prebuilt-email.md) [nummer](luis-reference-prebuilt-number.md)och [ordningstal.](luis-reference-prebuilt-ordinal.md)
