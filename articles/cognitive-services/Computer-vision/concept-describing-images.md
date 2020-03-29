---
title: Bildbeskrivningar - Datorseende
titleSuffix: Azure Cognitive Services
description: Begrepp relaterade till bildbeskrivningsfunktionen i API:et för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244723"
---
# <a name="describe-images-with-human-readable-language"></a>Beskriva bilder med läsbart språk som kan läsas av människor

Computer Vision kan analysera en bild och generera en mänsklig läsbar mening som beskriver dess innehåll. Algoritmen returnerar faktiskt flera beskrivningar baserat på olika visuella funktioner, och varje beskrivning får en konfidenspoäng. Den slutliga utdata är en lista över beskrivningar som beställts från högsta till lägsta förtroende.

## <a name="image-description-example"></a>Exempel på bildbeskrivning

Följande JSON-svar illustrerar vad Datorseende returnerar när du beskriver exempelbilden baserat på dess visuella funktioner.

![En svartvit bild av byggnader på Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Använda API:et

Bildbeskrivningsfunktionen är en del av [Api:et för analyseravbildning.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Du kan anropa det här API:et via en inbyggd SDK eller via REST-anrop. Inkludera `Description` i frågeparametern **visualFeatures.** Sedan, när du får hela JSON svar, helt enkelt tolka `"description"` strängen för innehållet i avsnittet.

* [Snabbstart: Datorseende .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Nästa steg

Lär dig de relaterade begreppen [att tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).
