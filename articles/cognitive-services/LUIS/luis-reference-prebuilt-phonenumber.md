---
title: Förbyggda telefonnummer för telefonnummer – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller fördefinierade entitetsinformation om telefonnummer i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: bf2fd053cabeaf85b177e284f86ba378e0e4389e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933424"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Fördefinierad telefonnummer enhet för en LUIS-app
Den `phonenumber` entiteten extraherar en mängd olika telefonnummer inklusive landskoden. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden i programmet. Den `phonenumber` entitet stöds i `en-us` kultur endast. 

## <a name="types-of-a-phone-number"></a>Typer av ett telefonnummer
`Phonenumber`hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Lösning för den här fördefinierade entiteten

### <a name="api-version-2x"></a>API-version 2. x

I följande exempel visas av lösningen på den **builtin.phonenumber** entitet.

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
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
}
```

### <a name="preview-api-version-3x"></a>Förhandsgranska API version 3. x

Följande JSON- `verbose` parameter har angetts till `false`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

Följande JSON- `verbose` parameter har angetts till `true`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
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
                        "score": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [procent](luis-reference-prebuilt-percentage.md), [nummer](luis-reference-prebuilt-number.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter. 
