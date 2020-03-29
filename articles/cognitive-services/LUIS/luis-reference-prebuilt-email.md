---
title: LUIS Fördefinierade entiteter e-postreferens
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information om fördefinierade enheter via e-post i Språkförståelse (LUIS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273482"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-postfördefinierad entitet för en LUIS-app
E-postextrahering innehåller hela e-postadressen från ett uttryck. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller e-post i programavsikterna. Endast e-postentitet stöds i `en-us` kulturen.

## <a name="resolution-for-prebuilt-email"></a>Lösning för fördefinierad e-post

Följande entitetsobjekt returneras för frågan:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)

Följande JSON är `verbose` med parametern inställd `true`på:

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

I följande exempel visas upplösningen för den **inbyggda.e-postentiteten.**

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

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [talet,](luis-reference-prebuilt-number.md) [ordningstal](luis-reference-prebuilt-ordinal.md)och [procent](luis-reference-prebuilt-percentage.md).
