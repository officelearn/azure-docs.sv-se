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
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270351"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL-fördefinierad entitet för en LUIS-app
URL-entiteten extraherar URL: er med domän namn eller IP-adresser. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller URL: er till programmet. URL-entiteten stöds `en-us` endast i kultur.

## <a name="types-of-urls"></a>Typer av URL: er
URL: en hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Lösning för fördefinierad URL-entitet

Följande enhets objekt returneras för frågan:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON- `verbose` parameter har angetts till `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)

Följande JSON- `verbose` parameter har angetts till `true`:

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
#### <a name="v2-response"></a>[V2-svar](#tab/V2)

I följande exempel visas lösningen https://www.luis.ai är ett exempel på en bra kognitiv tjänst exempel på artificiell intelligens

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
