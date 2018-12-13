---
title: PersonName fördefinierade entitet
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller personName fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 31dce870e99cbe74e2795a3ba661b0caf92dd48e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140242"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>PersonName fördefinierade entitet för en LUIS-app
Entiteten fördefinierade personName identifierar namn på kontakter. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller personName till programmet avsikter. personName entiteten har stöd för engelska och kinesiska [kulturer](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Lösning för personName entitet
I följande exempel visas av lösningen på den **builtin.personName** entitet.

```json
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