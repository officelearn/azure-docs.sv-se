---
title: Telefonnummer Fördefinierade enheter - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller telefonnummer fördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270474"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Telefonnummer fördefinierad entitet för en LUIS-app
Enheten `phonenumber` extraherar en mängd olika telefonnummer, inklusive landskod. Eftersom den här entiteten redan har tränats behöver du inte lägga till exempelyttranden i programmet. Entiteten `phonenumber` stöds `en-us` endast i kultur.

## <a name="types-of-a-phone-number"></a>Typer av telefonnummer
`Phonenumber`hanteras från [GitHub-databasen för identifierartext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Lösning för den här fördefinierade entiteten

Följande entitetsobjekt returneras för frågan:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)
Följande JSON är `verbose` med parametern inställd `true`på:

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

I följande exempel visas upplösningen för den **inbyggda.phonenumber-entiteten.**

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Lär dig mer om [procent-,](luis-reference-prebuilt-percentage.md) [tal-](luis-reference-prebuilt-number.md)och temperaturentiteter. [temperature](luis-reference-prebuilt-temperature.md)
