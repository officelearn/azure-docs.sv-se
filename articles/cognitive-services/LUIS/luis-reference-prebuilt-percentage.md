---
title: Den färdiga THOMAS entiteter procentandel referens - Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller procent färdiga entitetsinformation i språk förstå (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 4d502e93f8b6bf3af2da05a499c359faeabb51da
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321913"
---
# <a name="percentage-entity"></a>Procentandel entitet
Procentandel tal kan visas som bråk, `3 1/2`, eller som ett procentvärde, `2%`. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel utterances som innehåller procent för att programmet avsikter. Procentandel entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Typer av procent
Procentandel hanteras från den [identifierare text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) Github-lagringsplatsen

## <a name="resolution-for-prebuilt-percentage-entity"></a>Lösning för färdiga procentandel entitet
I följande exempel visas upplösning på **builtin.percentage** entitet.

```JSON
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