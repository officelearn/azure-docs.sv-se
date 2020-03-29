---
title: Ordnings och annan fördefinierad enhet - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller ordningsversion av fördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bb3bb27db48255f534e873ed4e93ac62f07016af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273453"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Ordningsordning för en LUIS-app
Ordningstal är en numerisk representation av ett `first`objekt `second` `third`i en uppsättning: , . Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden som innehåller ordnings av programavsikterna. Ordningsordning stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-ordinal"></a>Typer av ordningssorter
Ordningsvakt hanteras från [GitHub-databasen med identifierartext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Upplösning för fördefinierad ordningsenhet

Följande entitetsobjekt returneras för frågan:

`Order the second option`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)
Följande JSON är `verbose` med parametern inställd `true`på:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

I följande exempel visas upplösningen för den **inbyggda.ordningsarentiteten.**

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [OrdinalV2,](luis-reference-prebuilt-ordinal-v2.md) [telefonnummer](luis-reference-prebuilt-phonenumber.md)och temperatureniteter. [temperature](luis-reference-prebuilt-temperature.md)
