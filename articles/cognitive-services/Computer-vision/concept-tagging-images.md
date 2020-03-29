---
title: Innehållstaggar - Datorseende
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som är relaterade till bildtaggningsfunktionen i API:et för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244519"
---
# <a name="applying-content-tags-to-images"></a>Applicera innehållstaggar på bilder

Computer Vision returnerar taggar baserat på tusentals igenkännbara objekt, levande varelser, landskap och handlingar. I de fall där taggarna är tvetydiga eller dess betydelse inte är allmän kännedom ger API-svaren ”ledtrådar” för att tydliggöra taggarnas betydelse i en viss kontext. Taggar är inte ordnade som en taxonomi och det finns inga arvshierarkier. En samling innehållstaggar utgör grunden för en ”bildbeskrivning” som visas som språk som kan läsas av människor som är formaterade i fullständiga meningar. Observera att engelska för tillfället är det enda språket som stöds för bildbeskrivning.

När du laddar upp en bild eller anger en bild-URL, matar algoritmer för Visuellt innehåll ut taggar baserade på objekt, levande varelser och åtgärder som identifierats i bilden. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare.

## <a name="image-tagging-example"></a>Exempel på bildtaggning

Följande JSON-svar illustrerar vad Datorseende returnerar när visuella funktioner som identifieras i exempelbilden taggas.

![Ett blått hus och framsidan](./Images/house_yard.png).

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

## <a name="use-the-api"></a>Använda API:et

Taggningsfunktionen är en del av [Api:et för analyseravbildning.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Du kan anropa det här API:et via en inbyggd SDK eller via REST-anrop. Inkludera `Tags` i frågeparametern **visualFeatures.** Sedan, när du får hela JSON svar, helt enkelt tolka `"tags"` strängen för innehållet i avsnittet.

* [Snabbstart: Datorseende .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Nästa steg

Lär dig de relaterade begreppen [att kategorisera bilder](concept-categorizing-images.md) och beskriva [bilder](concept-describing-images.md).
