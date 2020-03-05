---
title: Fördefinierad Entity-LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller ålder fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 1607b80276a9c35bf0ac2f0a00bd2226e2c07f97
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270795"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Fördefinierad ålders enhet för en LUIS-app
Entiteten fördefinierade ålder samlar in värdet för ålder både numeriskt och när det gäller dagar, veckor, månader och år. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller ålder till programmet avsikter. Ålders enheten stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-age"></a>Typer av ålder
Ålder hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Lösning för fördefinierade ålder entitet



#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är med parametern `verbose` som har angetts till `false`:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON är med parametern `verbose` som har angetts till `true`:

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-svar](#tab/V2)

I följande exempel visas upplösningen för enheten **Builtin. Age** .

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om entiteterna [Currency](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)och [dimension](luis-reference-prebuilt-dimension.md) .
