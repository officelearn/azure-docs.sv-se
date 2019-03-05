---
title: 'URL: en fördefinierade entiteter'
titleSuffix: Azure
description: Den här artikeln innehåller en url fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 247720e8b1c044175d404dee6ffdc272ac3d24af
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336537"
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
