---
title: Som beskriver bilder - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för som beskriver bilder med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 423d1be57bc800108a08a81b72587ca2711bbc3d
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342423"
---
# <a name="describing-images"></a>Beskriva bilder

Datorn Vision algoritmer analysera innehållet i en bild. Den här analysen utgör grunden för en ”beskrivning” visas som läsbara språk i fullständiga meningar. Beskrivningen sammanfattar vad som finns i bild. Datorn Vision algoritmer generera olika beskrivningar baserat på visuella funktioner som identifierats i avbildningen. Varje beskrivning utvärderas och ett förtroenderesultat genereras. Sedan returneras en lista som är sorterad efter högsta till lägsta förtroendepoäng.

## <a name="image-description-example"></a>Exempel på Programbeskrivning bild

Följande JSON-svar visar vad för visuellt innehåll returnerar att beskriva exempelbild baserat på dess visuella funktioner.

![B & W byggnader](./Images/bw_buildings.png)

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