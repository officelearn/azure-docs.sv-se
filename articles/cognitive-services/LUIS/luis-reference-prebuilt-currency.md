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
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 369e8f94a4733a7c9e21fee98c72aa447802e943
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563502"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Fördefinierad valuta enhet för en LUIS-app
Den fördefinierade valuta enheten identifierar valuta i många valörer och länder/regioner, oavsett LUIS-programkultur. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller den valuta som programmet avsikter. Valuta entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typer av valuta
Valutan hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Lösning för valuta entitet

### <a name="api-version-2x"></a>API-version 2. x

I följande exempel visas av lösningen på den **builtin.currency** entitet.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
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
}
```



### <a name="preview-api-version-3x"></a>Förhandsgranska API version 3. x

Följande JSON- `verbose` parameter har angetts till `false`:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

Följande JSON- `verbose` parameter har angetts till `true`:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
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
    }
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md), och [e-post](luis-reference-prebuilt-email.md) entiteter. 
