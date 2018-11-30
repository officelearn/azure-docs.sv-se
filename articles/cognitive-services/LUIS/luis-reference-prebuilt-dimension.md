---
title: LUIS förskapade entiteter dimensionen referens – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller dimensionen fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 138cf1d2fbc050797c133afaffdb2a9820e2717f
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52332926"
---
# <a name="dimension-entity"></a>Entitet för dimension
Entiteten fördefinierade dimension identifierar olika typer av dimensioner, oavsett språkinställningar för LUIS-app. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller dimensioner till programmet avsikter. Dimensionen entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Typer av dimension

Dimensionen hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) Github-lagringsplats


## <a name="resolution-for-dimension-entity"></a>Lösning för dimensionen entitet
I följande exempel visas av lösningen på den **builtin.dimension** entitet.

```JSON
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

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [e-post](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md), och [ordningstal](luis-reference-prebuilt-ordinal.md) entiteter. 