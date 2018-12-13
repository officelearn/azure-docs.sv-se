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
ms.openlocfilehash: 5a1d86c58b6b6b8b54d170459157371e8e73f0e8
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163675"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>URL: en fördefinierade entitet för en LUIS-app
URL: en entitet extraherar URL: er med domännamn eller IP-adresser. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller URL: er till programmet. URL: en entitet har stöd för `en-us` kultur endast. 

## <a name="types-of-urls"></a>Typer av URL: er
URL: en hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub-lagringsplats

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