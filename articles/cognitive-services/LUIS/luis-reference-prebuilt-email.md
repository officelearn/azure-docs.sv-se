---
title: LUIS förskapade entiteter e-referens – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller e-post fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6d05f62ad725a89b0a34b21b8a8d36bb8fa464b1
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441019"
---
# <a name="email-entity"></a>Entitet för e-post
Extrahering av e-post innehåller hela e-postadressen från ett uttryck. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller e-postmeddelande till programmet avsikter. E-entitet stöds i `en-us` kultur endast. 

## <a name="resolution-for-prebuilt-email"></a>Lösning för fördefinierade e-post
I följande exempel visas av lösningen på den **builtin.email** entitet.

```JSON
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