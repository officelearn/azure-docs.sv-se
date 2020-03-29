---
title: Sammansatt entitetstyp - LUIS
titleSuffix: Azure Cognitive Services
description: En sammansatt entitet består av andra entiteter, till exempel fördefinierade entiteter, enkla, reguljära uttryck och listentiteter. De separata entiteterna utgör en hel entitet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695155"
---
# <a name="composite-entity"></a>Sammansatt entitet 

En sammansatt entitet består av andra entiteter, till exempel fördefinierade entiteter, enkla, reguljära uttryck och listentiteter. De separata entiteterna utgör en hel entitet. 

**Den här entiteten passar bra när data:**

* Är relaterade till varandra. 
* De är relaterade till varandra i yttrandet.
* Använd en mängd olika entitetstyper.
* Måste grupperas och bearbetas av klientprogrammet som en informationsenhet.
* Har en mängd olika användaryttranden som kräver maskininlärning.

![sammansatt enhet](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Exempel på JSON

Överväg en sammansatt entitet av fördefinierade `number` och `Location::ToLocation` med följande uttryck:

`book 2 tickets to cairo`

Observera `2`att , numret `cairo`och tolocationen har ord mellan dem som inte ingår i någon av entiteterna. Den gröna understrykningen, som används i [LUIS](luis-reference-regions.md) ett märkt uttryck på LUIS-webbplatsen, anger en sammansatt entitet.

![Sammansatt entitet](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

Sammansatta entiteter `compositeEntities` returneras i en matris och alla entiteter i kompositen returneras också i matrisen: `entities`

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

Detta är JSON `verbose=false` om anges i frågesträngen:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Detta är JSON `verbose=true` om anges i frågesträngen:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
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
|Fördefinierad entitet - tal|"byggt.nummer"|"2"|
|Förbyggd enhet - GeografiV2|"Plats::ToLocation"|"Kairo"|

## <a name="next-steps"></a>Nästa steg

I den här [självstudien](luis-tutorial-composite-entity.md)lägger du till en **sammansatt entitet** för att paketera extraherade data av olika typer i en enda innehållande entitet. Genom att bunta ihop data kan klientprogrammet enkelt extrahera relaterade data i olika datatyper.
