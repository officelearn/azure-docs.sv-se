---
title: 'LUIS fördefinierade entiteter URL: en referens – Azure | Microsoft Docs'
titleSuffix: Azure
description: Den här artikeln innehåller en url fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 1615d7e37119f185ebbb3bd674bc006d96d72e4d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030177"
---
# <a name="url-entity"></a>URL-entitet
URL: en entitet extraherar URL: er med domännamn eller IP-adresser. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller URL: er till programmet. URL: en entitet har stöd för `en-us` kultur endast. 

## <a name="types-of-urls"></a>Typer av URL: er
URL: en hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) Github-lagringsplats

## <a name="resolution-for-prebuilt-url-entity"></a>Lösning för den fördefinierade URL: en entitet
I följande exempel visas av lösningen på den **builtin.url** entitet.

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