---
title: URL-fördefinierade entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller fördefinierad entitets information för URL i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 12831ede2b9d9251f2e02fa396ee7d2fb2d61240
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499501"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL-fördefinierad entitet för en LUIS-app
URL-entiteten extraherar URL: er med domän namn eller IP-adresser. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller URL: er till programmet. URL-entiteten stöds endast i `en-us` kultur. 

## <a name="types-of-urls"></a>Typer av URL: er
URL: en hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Lösning för fördefinierad URL-entitet

Följande enhets objekt returneras för frågan:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-responsetabv3"></a>[V3-svar](#tab/V3)

Följande JSON är med parametern `verbose` som har angetts till `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 utförlig Response](#tab/V3-verbose)

Följande JSON är med parametern `verbose` som har angetts till `true`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
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

I följande exempel visas en lösning på https://www.luis.ai är ett exempel på en bra kognitiv tjänst exempel på artificiell intelligens

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * * 

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om entiteterna [ordning](luis-reference-prebuilt-ordinal.md), [antal](luis-reference-prebuilt-number.md)och [temperatur](luis-reference-prebuilt-temperature.md) .
