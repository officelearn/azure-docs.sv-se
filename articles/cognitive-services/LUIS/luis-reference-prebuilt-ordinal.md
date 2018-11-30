---
title: LUIS fördefinierade ordningstal entitetsreferenser – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller ordningstal fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: e80b9dc63accec43532116b81e6eb0b9bd52ee22
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335261"
---
# <a name="ordinal-entity"></a>Entitet för ordningstal
Ordningstal är en numerisk representation av ett objekt i en uppsättning: `first`, `second`, `third`. Eftersom den här entiteten har redan tränats, behöver du inte att lägga till exempel yttranden som innehåller ordningstal till programmet avsikter. Ordningstalet entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typer av nummer
Ordningstal hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) Github-lagringsplats

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Lösning för fördefinierade ordningstal entitet
I följande exempel visas av lösningen på den **builtin.ordinal** entitet.

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

Lär dig mer om den [procent](luis-reference-prebuilt-percentage.md), [phonenumber](luis-reference-prebuilt-phonenumber.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter. 