---
title: Ålder Fördefinierad enhet - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller åldersdefinierad entitetsinformation i Language Understanding (LUIS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270795"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Åldersfördefinierad entitet för en LUIS-app
Den fördefinierade åldersenheten fångar åldersvärdet både numeriskt och i form av dagar, veckor, månader och år. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller ålder i programavsikterna. Ålder enhet stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-age"></a>Typer av ålder
Ålder hanteras från [GitHub-databasen med identifierartext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Upplösning för fördefinierad åldersenhet



#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

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
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)
Följande JSON är `verbose` med parametern inställd `true`på:

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

I följande exempel visas upplösningen för den **inbyggda.age-entiteten.**

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

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [valuta,](luis-reference-prebuilt-currency.md) [datetimeV2](luis-reference-prebuilt-datetimev2.md)och dimensionsentiteter. [dimension](luis-reference-prebuilt-dimension.md)
