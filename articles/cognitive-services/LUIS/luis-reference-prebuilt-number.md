---
title: Antal fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller ett antal fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 5628c0fa3d6b3554fe096a323899770f3a14f97b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857200"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Antal fördefinierade entitet för en LUIS-app
Det finns många sätt som numeriska värden som används för att kvantifiera, express och beskriver typer av information. Den här artikeln beskriver bara några av exempel som möjligt. LUIS tolkar variationerna i användaren uttryck och returnerar konsekvent numeriska värden. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller tal som ska programmet avsikter. 

## <a name="types-of-number"></a>Typer av nummer
Antal hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub-lagringsplats

## <a name="examples-of-number-resolution"></a>Exempel på antalet upplösning

| Yttrande        | Entitet   | Lösning |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS innehåller värdet som känns igen av en **`builtin.number`** entitet i den `resolution` i JSON-svar returneras.

## <a name="resolution-for-prebuilt-number"></a>Lösning för fördefinierade nummer
I följande exempel visar ett JSON-svar från LUIS, som innehåller av lösningen på värdet 24, för uttryck ”24”.

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [valuta](luis-reference-prebuilt-currency.md), [ordningstal](luis-reference-prebuilt-ordinal.md), och [procent](luis-reference-prebuilt-percentage.md). 
