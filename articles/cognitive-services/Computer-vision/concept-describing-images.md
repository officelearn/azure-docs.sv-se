---
title: Som beskriver bilder - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för funktionen bild beskrivning av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 91618b211fdd869daf74491b175d6359ffa3f30c
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312199"
---
# <a name="describe-images-with-human-readable-language"></a>Beskriv bilder med läsbara språk

Visuellt innehåll kan analysera en bild och generera en läsbara mening som beskriver dess innehåll. Den algoritm som faktiskt retruns flera beskrivningar baserat på olika visuella funktioner och varje beskrivning får ett förtroenderesultat. Det slutgiltiga resultatet är en lista över beskrivningar som beställdes från högsta till lägsta förtroende.

## <a name="image-description-example"></a>Exempel på Programbeskrivning bild

Följande JSON-svar visar vad för visuellt innehåll returnerar att beskriva exempelbild baserat på dess visuella funktioner.

![En svart och bild av byggnader i Manhattan](./Images/bw_buildings.png)

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

Lär dig begrepp [tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).
