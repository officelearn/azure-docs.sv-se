---
title: PersonName fördefinierade enhet – LUIS Reference
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller personName fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0bc9d59fa44125429bfcb4e6ca9e68d93b81c04d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950581"
---
# <a name="personname-entity"></a>PersonName entitet
Entiteten fördefinierade personName identifierar namn på kontakter. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller personName till programmet avsikter. personName entiteten har stöd för engelska och kinesiska [kulturer](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Lösning för personName entitet
I följande exempel visas av lösningen på den **builtin.personName** entitet.

```JSON
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [e-post](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md), och [ordningstal](luis-reference-prebuilt-ordinal.md) entiteter. 