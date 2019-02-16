---
title: Identifiera ansikten - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för funktionen för identifiering av ansikte för den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1056b8be113d56342aea8f83d5325737f7ecb93b
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308459"
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
