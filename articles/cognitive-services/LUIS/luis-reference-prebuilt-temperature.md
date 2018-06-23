---
title: THOMAS färdiga entiteter temperatur referens - Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller temperatur färdiga entitetsinformation i språk förstå (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3cc16e7ec87775407c4261655d8f680cc0903e81
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321843"
---
# <a name="temperature-entity"></a>Temperatur entitet
Temperatur extraherar olika temperatur typer. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel utterances som innehåller temperatur till programmet. Temperatur-entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typer av temperatur
Temperatur hanteras från den [identifierare text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) Github-lagringsplatsen

## <a name="resolution-for-prebuilt-temperature-entity"></a>Lösning för färdiga temperatur entitet
I följande exempel visas upplösning på **builtin.temperature** entitet.

```JSON
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

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [procentandel](luis-reference-prebuilt-percentage.md), [nummer](luis-reference-prebuilt-number.md), och [ålder](luis-reference-prebuilt-age.md) entiteter. 