---
title: LUIS fördefinierade temperatur entitetsreferenser – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller temperatur fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 2716600346e65f2ca0b97e56cc156fdfb10ff3a4
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52440966"
---
# <a name="temperature-entity"></a>Entitet för temperatur
Temperatur extraherar en mängd olika typer av temperatur. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller temperatur till programmet. Temperatur entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Typer av temperatur
Temperatur hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) Github-lagringsplats

## <a name="resolution-for-prebuilt-temperature-entity"></a>Lösning för fördefinierade temperatur entitet
I följande exempel visas av lösningen på den **builtin.temperature** entitet.

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

Lär dig mer om den [procent](luis-reference-prebuilt-percentage.md), [nummer](luis-reference-prebuilt-number.md), och [ålder](luis-reference-prebuilt-age.md) entiteter. 