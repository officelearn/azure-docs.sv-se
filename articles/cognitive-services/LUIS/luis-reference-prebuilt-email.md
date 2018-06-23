---
title: Den färdiga THOMAS entiteter e-referens - Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller e-post färdiga entitetsinformation i språk förstå (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 517e92202bdd5bc8d970306b9e24999fa62a8e43
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321871"
---
# <a name="email-entity"></a>E-entitet
Extrahering av e-post innehåller hela e-postadressen från en utterance. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel utterances som innehåller e-postmeddelande till programmet avsikter. E-entitet stöds i `en-us` endast kultur. 

## <a name="resolution-for-prebuilt-email"></a>Lösning för fördefinierade e-post
I följande exempel visas upplösning på **builtin.email** entitet.

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

Lär dig mer om den [nummer](luis-reference-prebuilt-number.md), [ordningstal](luis-reference-prebuilt-ordinal.md), och [procentandel](luis-reference-prebuilt-percentage.md). 