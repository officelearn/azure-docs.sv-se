---
title: Enhets typ som har registrerats av enheten – LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017223"
---
# <a name="machine-learned-entity"></a>Enhet som har registrerats av enheten 



**Entiteten passar bra när text data:**


![lista entitet](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Exempel på JSON

Anta att appen har en lista som heter `Cities`, så att variationer av stadsnamn inklusive stad flygplats (Sea tac), flygplatsen (SEA), postnummer (98101), och phone riktnummer (206).

|Listobjekt|Objektet synonymer|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

I den föregående uttryck ordet `paris` mappas till paris-objektet som en del av den `Cities` lista entitet. Entiteten listan matchar både objektets normaliserade namn samt synonymer för objektet.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)


Detta är JSON om `verbose=false` anges i frågesträngen:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Detta är JSON om `verbose=true` anges i frågesträngen:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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
|Lista entitet|`Cities`|`paris`|


## <a name="next-steps"></a>Nästa steg

Lär dig mer om entiteten [lista](reference-entity-list.md) och [reguljära uttryck](reference-entity-regular-expression.md) .