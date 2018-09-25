---
title: Geografi V2 fördefinierade enhet – LUIS Reference
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller geographyV2 fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 3559bc02944f88f486104d4d9553f0c45a1f1754
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983420"
---
# <a name="geographyv2-entity"></a>GeographyV2 entitet
Entiteten fördefinierade geographyV2 identifierar platser. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden som innehåller GeographyV2 till programmet avsikter. GeographyV2 entiteten har stöd för engelska [kultur](luis-reference-prebuilt-entities.md).

## <a name="subtypes"></a>Undertyper
De geografiska platserna har undertyper:

|Undertyp|Syfte|
|--|--|
|`poi`|orienteringspunkt|
|`city`|namn på ort|
|`countryRegion`|namnet på land eller region|
|`continent`|namnet på kontinent|
|`state`|namnet på region|


## <a name="resolution-for-geographyv2-entity"></a>Lösning för GeographyV2 entitet
I följande exempel visas av lösningen på den **builtin.geographyV2** entitet.

```JSON
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [e-post](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md), och [ordningstal](luis-reference-prebuilt-ordinal.md) entiteter. 