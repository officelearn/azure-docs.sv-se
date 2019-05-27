---
title: Procentandel fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller procent fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 75538519b7d43aa702e15ce3c22ea4acc73ade87
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072204"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procentandel fördefinierade entitet för en LUIS-app
Procentandel siffrorna visas som bråkdelar, `3 1/2`, eller som procentandel, `2%`. Eftersom den här entiteten har redan tränats, behöver du inte att lägga till exempel yttranden som innehåller andel till programmet avsikter. Procentandel entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typer av procent
Procentandel hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub-lagringsplats

## <a name="resolution-for-prebuilt-percentage-entity"></a>Lösning för fördefinierade procent entitet

### <a name="api-version-2x"></a>API-version 2.x

I följande exempel visas av lösningen på den **builtin.percentage** entitet.

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Förhandsversionen av API 3.x

Följande JSON är med i `verbose` parameteruppsättning till `false`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ]
        }
    }
}
```

Följande JSON är med i `verbose` parameteruppsättning till `true`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ],
            "$instance": {
                "percentage": [
                    {
                        "type": "builtin.percentage",
                        "text": "2%",
                        "startIndex": 36,
                        "length": 2,
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

Lär dig mer om den [ordningstal](luis-reference-prebuilt-ordinal.md), [nummer](luis-reference-prebuilt-number.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter. 
