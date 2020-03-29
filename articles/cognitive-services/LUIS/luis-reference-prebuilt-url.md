---
title: URL fördefinierade enheter - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller url fördefinierade entitetsinformation i Language Understanding (LUIS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270351"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL-fördefinierad entitet för en LUIS-app
URL-entiteten extraherar webbadresser med domännamn eller IP-adresser. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller webbadresser till programmet. URL-entitet `en-us` stöds endast i kulturen.

## <a name="types-of-urls"></a>Typer av webbadresser
Url hanteras från [GitHub-databasen för identifierartext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Lösning för fördefinierad URL-entitet

Följande entitetsobjekt returneras för frågan:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)

Följande JSON är `verbose` med parametern inställd `true`på:

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

Följande exempel visar upplösningen https://www.luis.ai av är ett bra kognitivt tjänste-exempel på artificiell intelligens

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

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [number](luis-reference-prebuilt-number.md) [ordnings-](luis-reference-prebuilt-ordinal.md) [och](luis-reference-prebuilt-temperature.md) temperatureniteter.
