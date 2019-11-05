---
title: PersonName-fördefinierad entitet – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller personName för inbyggd entitet i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9777c62d97c70d4f6a0d0a4d912dea3fa8decd23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499541"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName-fördefinierad entitet för en LUIS-app
Den förinställda personName-entiteten identifierar person namn. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller personName till program avsikterna. personName-entiteten stöds på engelska och kinesiska [kulturer](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Lösning för personName-entitet

Följande enhets objekt returneras för frågan:

`Is Jill Jones in Cairo?`


#### <a name="v3-responsetabv3"></a>[V3-svar](#tab/V3)


Följande JSON är med parametern `verbose` som har angetts till `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON är med parametern `verbose` som har angetts till `true`:

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
#### <a name="v2-responsetabv2"></a>[V2-svar](#tab/V2)

I följande exempel visas upplösningen för entiteten **Builtin. personName** .

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

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om entiteterna [e-post](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md)och [ordnings tal](luis-reference-prebuilt-ordinal.md) . 
