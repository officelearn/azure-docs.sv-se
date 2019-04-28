---
title: Ordningstalet fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller ordningstal fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 4992ca9d1a09fa751697d6608400eb4dda688108
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473176"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Ordningstalet fördefinierade entitet för en LUIS-app
Ordningstal är en numerisk representation av ett objekt i en uppsättning: `first`, `second`, `third`. Eftersom den här entiteten har redan tränats, behöver du inte att lägga till exempel yttranden som innehåller ordningstal till programmet avsikter. Ordningstalet entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typer av nummer
Ordningstal hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub-lagringsplats

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Lösning för fördefinierade ordningstal entitet
I följande exempel visas av lösningen på den **builtin.ordinal** entitet.

```json
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
