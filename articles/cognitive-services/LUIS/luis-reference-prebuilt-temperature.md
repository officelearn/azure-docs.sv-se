---
title: Fördefinierad temperatur enhet – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller temperatur fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: ecdaec6dcade033bf99842dd384be095dd363a05
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933409"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Fördefinierad temperatur enhet för en LUIS-app
Temperatur extraherar en mängd olika typer av temperatur. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller temperatur till programmet. Temperatur entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typer av temperatur
Temperaturen hanteras från Recognizer [-text GitHub-](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) lagringsplatsen

## <a name="resolution-for-prebuilt-temperature-entity"></a>Lösning för fördefinierade temperatur entitet

### <a name="api-version-2x"></a>API-version 2. x

I följande exempel visas av lösningen på den **builtin.temperature** entitet.

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Förhandsgranska API version 3. x

Följande JSON- `verbose` parameter har angetts till `false`:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ]
        }
    }
}
```

Följande JSON- `verbose` parameter har angetts till `true`:

```json
{
    "query": "set the temperature to 30 degrees",
    "prediction": {
        "normalizedQuery": "set the temperature to 30 degrees",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.656305432
            }
        },
        "entities": {
            "temperature": [
                {
                    "number": 30,
                    "unit": "Degree"
                }
            ],
            "$instance": {
                "temperature": [
                    {
                        "type": "builtin.temperature",
                        "text": "30 degrees",
                        "startIndex": 23,
                        "length": 10,
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

Lär dig mer om den [procent](luis-reference-prebuilt-percentage.md), [nummer](luis-reference-prebuilt-number.md), och [ålder](luis-reference-prebuilt-age.md) entiteter. 
