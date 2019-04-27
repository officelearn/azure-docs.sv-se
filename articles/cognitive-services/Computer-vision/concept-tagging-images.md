---
title: Tillämpa avbildningar - visuellt innehåll taggar
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp för avbildningarna taggningsfunktion för den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
origin.date: 02/08/2019
ms.date: 02/27/2019
ms.author: v-junlch
ms.custom: seodec18
ms.openlocfilehash: aeb03566a650fe46286d77913e0d36dcbb19f436
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759681"
---
# <a name="applying-content-tags-to-images"></a>Använda innehåll taggar till avbildningar

Visuellt innehåll returnerar taggar som baseras på tusentals identifierbara objekt, levande varelser, landskap och åtgärder. I de fall där taggarna är tvetydiga eller dess betydelse inte är allmän kännedom ger API-svaren ”ledtrådar” för att tydliggöra taggarnas betydelse i en viss kontext. Taggar är inte ordnade som en taxonomi och det finns inga arvshierarkier. En samling innehållstaggar utgör grunden för en ”bildbeskrivning” som visas som språk som kan läsas av människor som är formaterade i fullständiga meningar. Observera att engelska för tillfället är det enda språket som stöds för bildbeskrivning.

När du laddar upp en bild eller ange en bild-URL, utdata visuellt algoritmer taggar baserat på de objekt, levande varelser och åtgärder som identifierats i avbildningen. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare.

## <a name="image-tagging-example"></a>Bild taggning exempel

Följande JSON-svar visar vad för visuellt innehåll returnerar när taggning visuella funktioner som har identifierats i exempelbild.

![En blå house och framsida](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [kategorisera bilder](concept-categorizing-images.md) och [som beskriver avbildningar](concept-describing-images.md).

