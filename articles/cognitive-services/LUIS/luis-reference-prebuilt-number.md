---
title: THOMAS färdiga entiteter number referens - Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller fördefinierade entitetsinformation om språk förstå (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: aa0b389a0694a3b742259fd42bed08055fbbadbe
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321864"
---
# <a name="number-entity"></a>Antalet entitet
Det finns många sätt som numeriska värden som används för att kvantifiera express och beskriver uppgifter. Den här artikeln beskrivs bara några möjliga exemplen. THOMAS tolkar variationer i användaren utterances och returnerar konsekvent numeriska värden. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel utterances som innehåller tal som ska programmet avsikter. 

## <a name="types-of-number"></a>Typer av tal
Antal hanteras från den [identifierare text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) Github-lagringsplatsen

## <a name="examples-of-number-resolution"></a>Exempel på antalet upplösning

| utterance        | Entitet   | Lösning |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


THOMAS innehåller tolkade värdet för en **`builtin.number`** entitet i den `resolution` i JSON-svar returneras.

## <a name="resolution-for-prebuilt-number"></a>Lösning för färdiga nummer
I följande exempel visas en JSON-svar från THOMAS, som innehåller värdet 24 för utterance ”24” upplösning.

```JSON
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

Lär dig mer om den [valuta](luis-reference-prebuilt-currency.md), [ordningstal](luis-reference-prebuilt-ordinal.md), och [procentandel](luis-reference-prebuilt-percentage.md). 