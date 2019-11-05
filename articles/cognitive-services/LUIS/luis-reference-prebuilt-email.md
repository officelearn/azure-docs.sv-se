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
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6f262752a50b58eae8ffbea81b8e7fc4d8c65b98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464975"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Fördefinierad e-postentitet för en LUIS-app
E-postextraheringen innehåller hela e-postadressen från en uttryck. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller e-post till program avsikterna. E-postentiteten stöds endast i `en-us` kultur. 

## <a name="resolution-for-prebuilt-email"></a>Lösning för fördefinierad e-post

Följande enhets objekt returneras för frågan:

`please send the information to patti@contoso.com`

#### <a name="v3-responsetabv3"></a>[V3-svar](#tab/V3)

Följande JSON är med parametern `verbose` som har angetts till `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 utförlig Response](#tab/V3-verbose)

Följande JSON är med parametern `verbose` som har angetts till `true`:

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
#### <a name="v2-responsetabv2"></a>[V2-svar](#tab/V2)

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
