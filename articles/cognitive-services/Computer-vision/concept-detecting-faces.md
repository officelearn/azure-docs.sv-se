---
title: Identifiera ansikten - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp för funktionen för identifiering av ansikte för den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 699192aba87bb009d7dbddddcc9579883bb71db9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368143"
---
# <a name="face-detection-with-computer-vision"></a>Ansiktsigenkänning med visuellt innehåll

Visuellt innehåll kan identifiera ansikten i en bild och generera ålder, kön och rektangel för varje identifierad ansikte. 

> [!NOTE]
> Den här funktionen är även erbjuds av Azure [ansikte](/azure/cognitive-services/face/) service. Se detta alternativ för mer detaljerad analys, inklusive ansiktsidentifiering för ansiktsigenkänning och utgöra identifiering. 

## <a name="face-detection-examples"></a>Exempel för identifiering av ansikte

I följande exempel visas JSON-svaret som returnerades av visuellt innehåll för en avbildning som innehåller ett enda mänskliga ansikte.

![Visuellt innehåll analyserar kvinnoansikte på tak](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

I nästa exempel visas JSON-svar returneras för en avbildning som innehåller flera ansikten.

![Visuellt innehåll analyserar Family foto ansikte](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Se den [analysera bild](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) referensdokumentation mer information om hur du använder funktionen för identifiering av ansikte.
