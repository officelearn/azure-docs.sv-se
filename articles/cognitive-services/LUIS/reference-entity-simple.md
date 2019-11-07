---
title: Enkel entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: En enkel entitet beskriver ett enda koncept från den dator som har lärts. Lägg till en fras lista när du använder en enkel entitet för att förbättra resultaten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 27eb4d827c1488b0d051505e62286fd66d6a286b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671505"
---
# <a name="simple-entity"></a>Enkel entitet 

En enkel entitet är en generisk entitet som beskriver ett enda koncept och som har lärts från den dator som har lärts. Eftersom enkla entiteter vanligt vis är namn, till exempel företags namn, produkt namn eller andra kategorier av namn, lägger du till en [fras lista](luis-concept-feature.md) när du använder en enkel entitet för att öka signalen av de namn som används. 

**Entiteten passar bra när:**

* Data formateras inte konsekvent, utan anger samma sak. 

![enkel entitet](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Exempel-JSON

`Bob Jones wants 3 meatball pho`

I föregående uttryck är `Bob Jones` märkta som en enkel `Customer` entitet.

Data som returneras från slut punkten innehåller enhets namnet, den identifierade texten från uttryck, platsen för den identifierade texten och poängen:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Detta är JSON om `verbose=false` anges i frågesträngen:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Data objekt|Entitetsnamn|Värde|
|--|--|--|
|Enkel entitet|`Customer`|`bob jones`|

## <a name="next-steps"></a>Nästa steg

I den här [självstudien](luis-quickstart-primary-and-secondary-data.md)extraherar du data från en uttryck med hjälp av den **enkla entiteten**. Om du vill öka extraherings precisionen lägger du till en [fras lista](luis-concept-feature.md) med termer som är speciella för den enkla entiteten.