---
title: PersonName-fördefinierad entitet – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller personName fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b5f4855c03c1c003df8f58b135cb809f1757e58f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677473"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName-fördefinierad entitet för en LUIS-app
Entiteten fördefinierade personName identifierar namn på kontakter. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller personName till programmet avsikter. personName entiteten har stöd för engelska och kinesiska [kulturer](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Lösning för personName entitet

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

I följande exempel visas av lösningen på den **builtin.personName** entitet.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  },
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```
#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)


Följande JSON är med parametern `verbose` inställd på `false`:

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ]
        }
    }
}
```

Följande JSON är med parametern `verbose` inställd på `true`:

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ],
            "$instance": {
                "personName": [
                    {
                        "type": "builtin.personName",
                        "text": "Jill Jones",
                        "startIndex": 3,
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

* * * 

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om den [e-post](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md), och [ordningstal](luis-reference-prebuilt-ordinal.md) entiteter. 
