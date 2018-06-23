---
title: 'THOMAS färdiga entiteter URL: en referens - Azure | Microsoft Docs'
titleSuffix: Azure
description: 'Den här artikeln innehåller URL: en fördefinierad entitetsinformation i språk förstå (THOMAS).'
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 4eacf564a295a568a3e2c8d2f44ad0af3fbbe258
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321962"
---
# <a name="url-entity"></a>URL: en entitet
URL: en entitet hämtar URL: er med domännamn eller IP-adresser. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel utterances som innehåller URL: er till programmet. URL-entitet stöds i `en-us` endast kultur. 

## <a name="types-of-urls"></a>URL-typer
URL: en hanteras från den [identifierare text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) Github-lagringsplatsen

## <a name="resolution-for-prebuilt-url-entity"></a>Lösning för färdiga URL: en entitet
I följande exempel visas upplösning på **builtin.url** entitet.

```JSON
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [ordningstal](luis-reference-prebuilt-ordinal.md), [nummer](luis-reference-prebuilt-number.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter.