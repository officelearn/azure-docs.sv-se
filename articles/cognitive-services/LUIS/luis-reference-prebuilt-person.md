---
title: Fördefinierad entitet för personnamn - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller personName fördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273422"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Fördefinierad entitet för personnamn för en LUIS-app
Den fördefinierade personName-entiteten identifierar personnamn. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller personnamn i programavsikterna. personName enhet stöds i engelska och kinesiska [kulturer](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Lösning för personName-entitet

Följande entitetsobjekt returneras för frågan:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[V3-svar](#tab/V3)


Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)
Följande JSON är `verbose` med parametern inställd `true`på:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[V2-svar](#tab/V2)

I följande exempel visas upplösningen för den **inbyggda.personName-entiteten.**

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Läs mer om [e-post,](luis-reference-prebuilt-email.md) [nummer](luis-reference-prebuilt-number.md)och [ordningstal.](luis-reference-prebuilt-ordinal.md)
