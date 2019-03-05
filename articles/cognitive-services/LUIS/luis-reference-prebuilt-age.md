---
title: Ålder fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller ålder fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: c901a384d7629a187a2a4fbd4295a1a68615abdb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342606"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Ålder fördefinierade entitet för en LUIS-app
Entiteten fördefinierade ålder samlar in värdet för ålder både numeriskt och när det gäller dagar, veckor, månader och år. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller ålder till programmet avsikter. Ålder-entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typer av ålder
Ålder hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub-lagringsplats

## <a name="resolution-for-prebuilt-age-entity"></a>Lösning för fördefinierade ålder entitet
I följande exempel visas av lösningen på den **builtin.age** entitet.

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [valuta](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), och [dimension](luis-reference-prebuilt-dimension.md) entiteter. 
