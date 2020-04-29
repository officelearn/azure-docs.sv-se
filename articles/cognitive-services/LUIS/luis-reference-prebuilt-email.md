---
title: E-postreferens för LUIS-fördefinierade entiteter
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller fördefinierad enhets information för e-post i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f7aa2a341a82d30f7022af49e626a3cd358854b7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273482"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Fördefinierad e-postentitet för en LUIS-app
E-postextraheringen innehåller hela e-postadressen från en uttryck. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller e-post till program avsikterna. E-postentiteten `en-us` stöds endast i kultur.

## <a name="resolution-for-prebuilt-email"></a>Lösning för fördefinierad e-post

Följande enhets objekt returneras för frågan:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON- `verbose` parameter har angetts till `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)

Följande JSON- `verbose` parameter har angetts till `true`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
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

I följande exempel visas upplösningen för entiteten **Builtin. e-post** .

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om [talet](luis-reference-prebuilt-number.md), [ordnings talet](luis-reference-prebuilt-ordinal.md)och [procent andelen](luis-reference-prebuilt-percentage.md).
