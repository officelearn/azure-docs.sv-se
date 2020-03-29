---
title: Enkel entitetstyp - LUIS
titleSuffix: Azure Cognitive Services
description: En enkel entitet beskriver ett enda koncept från det maskininlärda kontexten. Lägg till en fraslista när du använder en enkel entitet för att förbättra resultaten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74894764"
---
# <a name="simple-entity"></a>Enkel entitet

En enkel entitet är en generisk entitet som beskriver ett enda koncept och lärs ut från den maskininlärda kontexten. Eftersom enkla entiteter i allmänhet är namn som företagsnamn, produktnamn eller andra kategorier av namn lägger du till en [fraslista](luis-concept-feature.md) när du använder en enkel entitet för att öka signalen för de namn som används.

**Entiteten passar bra när:**

* Data är inte konsekvent formaterade men anger samma sak.

![enkel enhet](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Exempel på JSON

`Bob Jones wants 3 meatball pho`

I föregående uttryck, `Bob Jones` är märkt som `Customer` en enkel entitet.

De data som returneras från slutpunkten innehåller entitetsnamnet, den identifierade texten från uttryck, platsen för den identifierade texten och poängen:

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

Detta är JSON `verbose=false` om anges i frågesträngen:

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
|Enkel entitet|`Customer`|`bob jones`|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mönstersyntax](reference-pattern-syntax.md)