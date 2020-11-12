---
title: Bild beskrivningar – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp relaterade till bild beskrivnings funktionen i API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7d2b87710b2590f9bcf511ec0cbd5637913fb5c4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538452"
---
# <a name="describe-images-with-human-readable-language"></a>Beskriv bilder med läsligt språk

Visuellt innehåll kan analysera en bild och generera en läslig mening som beskriver dess innehåll. Algoritmen returnerar flera beskrivningar baserat på olika visuella funktioner, och varje beskrivning får en säkerhets poäng. Slutgiltiga utdata är en lista över beskrivningar som har beställts från högst till lägsta förtroende.

## <a name="image-description-example"></a>Bild beskrivnings exempel

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du beskriver exempel bilden baserat på dess visuella funktioner.

![En svart och vit bild av byggnader i Manhattan](./Images/bw_buildings.png)

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

Funktionen bild beskrivning är en del av API: et [analys av avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Du kan anropa API: et via en inbyggd SDK eller via REST-anrop. Inkludera `Description` i **visualFeatures** -Frågeparametern. När du sedan får det fullständiga JSON-svaret ska du bara parsa strängen för innehållet i `"description"` avsnittet.

* [Snabb start: Visuellt innehåll .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabb start: analysera en avbildning (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Nästa steg

Lär dig relaterade begrepp för att [Tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).
