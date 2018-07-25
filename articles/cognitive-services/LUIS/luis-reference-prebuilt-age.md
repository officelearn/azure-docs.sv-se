---
title: LUIS förskapade entiteter ålder referens – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller ålder fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: f93acb8bece8c66c3ed7197f1c4530011aec3f29
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237408"
---
# <a name="age-entity"></a>Entitet för ålder
Entiteten fördefinierade ålder samlar in värdet för ålder både numeriskt och när det gäller dagar, veckor, månader och år. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller ålder till programmet avsikter. Ålder-entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Typer av ålder
Ålder hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) Github-lagringsplats

## <a name="resolution-for-prebuilt-age-entity"></a>Lösning för fördefinierade ålder entitet
I följande exempel visas av lösningen på den **builtin.age** entitet.

```JSON
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