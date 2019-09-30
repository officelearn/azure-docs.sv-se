---
title: Ordnings fördefinierad entitet – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller ordningstal fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: d1d69b256c4fc7e7b9d1c84b7c409d01a9f8ce52
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677547"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Fördefinierad ordnings enhet för en LUIS-app
Ordningstal är en numerisk representation av ett objekt i en uppsättning: `first`, `second`, `third`. Eftersom den här entiteten har redan tränats, behöver du inte att lägga till exempel yttranden som innehåller ordningstal till programmet avsikter. Ordningstalet entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typer av nummer
Ordnings talet hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Lösning för fördefinierade ordningstal entitet

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

I följande exempel visas av lösningen på den **builtin.ordinal** entitet.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
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
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Följande JSON är med parametern `verbose` inställd på `false`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

Följande JSON är med parametern `verbose` inställd på `true`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
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
    }
}
```

* * * 

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [telefonnummer](luis-reference-prebuilt-phonenumber.md)och [temperatur](luis-reference-prebuilt-temperature.md) enheter. 
