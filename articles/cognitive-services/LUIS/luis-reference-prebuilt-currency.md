---
title: Valutaförbyggd entitet - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information om fördefinierad entitet i språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 56d264fa976967ae4772f50b4aa6f58b30355d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270771"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Valutafördefinierad entitet för en LUIS-app
Den fördefinierade valutaentiteten identifierar valuta i många valörer och länder/regioner, oavsett LUIS-appkultur. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller valuta i programavsikterna. Valutaentitet stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-currency"></a>Typer av valuta
Valutan hanteras från [GitHub-databasen med identifierartext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Upplösning för valutaentitet

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)
Följande JSON är `verbose` med parametern inställd `true`på:

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-svar](#tab/V2)

I följande exempel visas upplösningen för den **inbyggda.valutaentiteten.**

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [datetimeV2,](luis-reference-prebuilt-datetimev2.md) [dimension](luis-reference-prebuilt-dimension.md)och [e-postentiteter.](luis-reference-prebuilt-email.md)
