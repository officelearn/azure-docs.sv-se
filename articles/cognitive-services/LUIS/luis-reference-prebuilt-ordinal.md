---
title: Den färdiga THOMAS entiteter ordningstal referens - Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller entitetsinformation om ordningstal färdiga i språk förstå (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 2ff9b083e6cabe455baea3ed777dd6cc00b6fbfe
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321969"
---
# <a name="ordinal-entity"></a>Ordningstalet entitet
Ordningstalet är en numerisk representation av ett objekt i en uppsättning: `first`, `second`, `third`. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel utterances som innehåller ordningstal till programmet avsikter. Ordningstalet entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typer av ordningstal
Ordningstalet hanteras från den [identifierare text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) Github-lagringsplatsen

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Lösning för färdiga ordningstal entitet
I följande exempel visas upplösning på **builtin.ordinal** entitet.

```JSON
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [procentandel](luis-reference-prebuilt-percentage.md), [phonenumber](luis-reference-prebuilt-phonenumber.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter. 