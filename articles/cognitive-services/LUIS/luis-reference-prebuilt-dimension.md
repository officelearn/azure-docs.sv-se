---
title: Fördefinierade dimensions entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller dimensionen fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2a2654947ab43000613cb1076b41d2ff5c2180e9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560269"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Fördefinierad dimensions enhet för en LUIS-app
Entiteten fördefinierade dimension identifierar olika typer av dimensioner, oavsett språkinställningar för LUIS-app. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller dimensioner till programmet avsikter. Dimensionen entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typer av dimension

Dimensionen hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)


## <a name="resolution-for-dimension-entity"></a>Lösning för dimensionen entitet

### <a name="api-version-2x"></a>API-version 2. x

I följande exempel visas av lösningen på den **builtin.dimension** entitet.

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Förhandsgranska API version 3. x

Följande JSON- `verbose` parameter har angetts till `false`:

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ]
        }
    }
}
```

Följande JSON- `verbose` parameter har angetts till `true`:

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ],
            "$instance": {
                "dimension": [
                    {
                        "type": "builtin.dimension",
                        "text": "10 1/2 miles",
                        "startIndex": 19,
                        "length": 12,
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

Lär dig mer om den [e-post](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md), och [ordningstal](luis-reference-prebuilt-ordinal.md) entiteter. 
