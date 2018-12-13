---
title: AktuellY fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller valuta fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: c87bfddb611e3e232d4a43f45ae60412566b98fc
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161771"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Valuta fördefinierade entitet för en LUIS-app
Valutaentiteten fördefinierade identifierar valuta i många benämningar och länder/regioner, oavsett LUIS app kultur. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller den valuta som programmet avsikter. Valuta entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typer av valuta
Valuta hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) GitHub-lagringsplats

## <a name="resolution-for-currency-entity"></a>Lösning för valuta entitet
I följande exempel visas av lösningen på den **builtin.currency** entitet.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md), och [e-post](luis-reference-prebuilt-email.md) entiteter. 