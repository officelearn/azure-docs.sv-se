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
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894764"
---
# <a name="simple-entity"></a>Enkel entitet

En enkel entitet är en generisk entitet som beskriver ett enda koncept och som har lärts från den dator som har lärts. Eftersom enkla entiteter vanligt vis är namn, till exempel företags namn, produkt namn eller andra kategorier av namn, lägger du till en [fras lista](luis-concept-feature.md) när du använder en enkel entitet för att öka signalen av de namn som används.

**Entiteten passar bra när:**

* Data formateras inte konsekvent, utan anger samma sak.

![enkel enhet](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Exempel på JSON

`Bob Jones wants 3 meatball pho`

I den föregående uttryck `Bob Jones` är märkt som en enkel `Customer` entitet.

De data som returneras från slutpunkten innehåller entitetsnamnet, identifierade texten från uttryck, platsen för den identifierade texten och poängen:

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

|Dataobjekt|Entitetsnamn|Värde|
|--|--|--|
|Enkel enhet|`Customer`|`bob jones`|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mönstermatchningssyntax](reference-pattern-syntax.md)