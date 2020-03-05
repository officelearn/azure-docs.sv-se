---
title: Fördefinierad valuta entitet – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller valuta fördefinierade entitetsinformation i Språkförståelse (LUIS).
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270771"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Fördefinierad valuta enhet för en LUIS-app
Den fördefinierade valuta enheten identifierar valuta i många valörer och länder/regioner, oavsett LUIS-programkultur. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller den valuta som programmet avsikter. Currency-entiteten stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-currency"></a>Typer av valuta
Valutan hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Lösning för valuta entitet

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är med parametern `verbose` som har angetts till `false`:

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
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON är med parametern `verbose` som har angetts till `true`:

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

I följande exempel visas upplösningen för entiteten **Builtin. Currency** .

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

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md)och [e-](luis-reference-prebuilt-email.md) postentiteter.
