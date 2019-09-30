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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 9e237b09bd2b0ecc3e91e10697f6c025f73057da
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677443"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Fördefinierad telefonnummer enhet för en LUIS-app
Den `phonenumber` entiteten extraherar en mängd olika telefonnummer inklusive landskoden. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden i programmet. Den `phonenumber` entitet stöds i `en-us` kultur endast. 

## <a name="types-of-a-phone-number"></a>Typer av ett telefonnummer
`Phonenumber` hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)

## <a name="resolution-for-this-prebuilt-entity"></a>Lösning för den här fördefinierade entiteten

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Följande JSON är med parametern `verbose` inställd på `false`:

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

Följande JSON är med parametern `verbose` inställd på `true`:

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

* * * 

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om den [procent](luis-reference-prebuilt-percentage.md), [nummer](luis-reference-prebuilt-number.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter. 
