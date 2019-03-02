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
ms.date: 02/28/2018
ms.author: diberry
ms.openlocfilehash: 7f2808913178eb1d9a3de78b9f4948ee8031e4f4
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214426"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Procentandel fördefinierade entitet för en LUIS-app
Procentandel siffrorna visas som bråkdelar, `3 1/2`, eller som procentandel, `2%`. Eftersom den här entiteten har redan tränats, behöver du inte att lägga till exempel yttranden som innehåller andel till programmet avsikter. Procentandel entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typer av procent
Procentandel hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub-lagringsplats

## <a name="resolution-for-prebuilt-percentage-entity"></a>Lösning för fördefinierade procent entitet
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

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [ordningstal](luis-reference-prebuilt-ordinal.md), [nummer](luis-reference-prebuilt-number.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter. 
