---
title: Ordningsman v2 fördefinierad enhet - LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller ordningsman v2 fördefinierad entitetsinformation i Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270490"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Ordningsman för fördefinierad entitet för en LUIS-app
Ordningstalet V2 expanderar [Ordningstalet](luis-reference-prebuilt-ordinal.md) för att `next` `last`ange `previous`relativa referenser som , och . Dessa extraheras inte med hjälp av den ordinala fördefinierade entiteten.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Upplösning för fördefinierad ordningsordning V2

Följande entitetsobjekt returneras för frågan:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON är `verbose` med parametern inställd `false`på:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3-verbos respons](#tab/V3-verbose)

Följande JSON är `verbose` med parametern inställd `true`på:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2-svar](#tab/V2)

I följande exempel visas upplösningen för den **inbyggda.ordinalV2-entiteten.**

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).

Läs mer om [procent-](luis-reference-prebuilt-percentage.md) [och telefonnummer](luis-reference-prebuilt-phonenumber.md)och temperatureniteter. [temperature](luis-reference-prebuilt-temperature.md)
