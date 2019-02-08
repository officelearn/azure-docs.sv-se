---
title: LUIS förskapade entiteter e-referens – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller e-post fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 51e6a5da0d757023bd7cd1f61c77387a77b77de4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869066"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-fördefinierade entitet för en LUIS-app
Extrahering av e-post innehåller hela e-postadressen från ett uttryck. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller e-postmeddelande till programmet avsikter. E-entitet stöds i `en-us` kultur endast. 

## <a name="resolution-for-prebuilt-email"></a>Lösning för fördefinierade e-post
I följande exempel visas av lösningen på den **builtin.email** entitet.

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [nummer](luis-reference-prebuilt-number.md), [ordningstal](luis-reference-prebuilt-ordinal.md), och [procent](luis-reference-prebuilt-percentage.md). 
