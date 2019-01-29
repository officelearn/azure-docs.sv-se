---
title: Som beskriver bilder - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för funktionen bild beskrivning av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 933c4e4a6731a492a5aa461de7c782af422fb6fb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162709"
---
# <a name="describe-images-with-human-readable-language"></a>Beskriv bilder med läsbara språk

Datorn Vision algoritmer analysera innehållet i en bild. Denna analys utgör grunden för en ”bildbeskrivning” som visas som språk som kan läsas av människor i fullständiga meningar. Beskrivningen sammanfattar vad som finns i bilden. Datorn Vision algoritmer generera olika beskrivningar baserat på visuella funktioner som identifierats i avbildningen. Varje beskrivning utvärderas och ett förtroenderesultat genereras. Sedan returneras en lista som är sorterad efter högsta till lägsta förtroendepoäng.

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
