---
title: LUIS fördefinierade valuta entitetsreferenser – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller valuta fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 89350ad6a49ceef8169d1693dafdedf702992504
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033897"
---
# <a name="currency-entity"></a>Entitet för valuta
Valutaentiteten fördefinierade identifierar valuta i många benämningar och länder/regioner, oavsett LUIS app kultur. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller den valuta som programmet avsikter. Valuta entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Typer av valuta
Valuta hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) Github-lagringsplats

## <a name="resolution-for-currency-entity"></a>Lösning för valuta entitet
I följande exempel visas av lösningen på den **builtin.currency** entitet.

```JSON
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