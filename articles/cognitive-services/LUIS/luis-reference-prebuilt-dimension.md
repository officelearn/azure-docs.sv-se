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
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: e9fd22bd89e79d0b3f785b7743c103b6955f828c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034696"
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