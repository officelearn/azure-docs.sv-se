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
ms.openlocfilehash: dcf61c642a9f8ad7aa68d72736ce8fdb0e9a6e3c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945268"
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

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om att [Tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).
