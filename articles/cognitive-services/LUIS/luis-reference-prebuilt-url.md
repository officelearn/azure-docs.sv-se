---
title: 'URL: en fördefinierade entiteter'
titleSuffix: Azure
description: Den här artikeln innehåller en url fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: e01bcd022b5688b6cf00f63fcf4a0f06a477ebed
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093632"
---
# <a name="url-entity"></a>URL-entitet
URL: en entitet extraherar URL: er med domännamn eller IP-adresser. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller URL: er till programmet. URL: en entitet har stöd för `en-us` kultur endast. 

## <a name="types-of-urls"></a>Typer av URL: er
URL: en hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) Github-lagringsplats

## <a name="resolution-for-prebuilt-url-entity"></a>Lösning för den fördefinierade URL: en entitet
I följande exempel visas av lösningen på den **builtin.url** entitet.

```json
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