---
title: Ansikts identifiering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör funktionen för ansikts igenkänning i API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3675acf59c6889dd00c8f26089f509bbcd3c724e
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945249"
---
# <a name="face-detection-with-computer-vision"></a>Ansikts igenkänning med Visuellt innehåll

Visuellt innehåll kan identifiera mänskliga ansikten i en bild och generera ålder, kön och rektangeln för varje identifierad ansikte. 

> [!NOTE]
> Den här funktionen erbjuds även av Azure [Face](/azure/cognitive-services/face/) service. Se det här alternativet för mer detaljerad ansikts analys, inklusive identifiering av ansikte och identifiering. 

## <a name="face-detection-examples"></a>Exempel på ansikts igenkänning

I följande exempel demonstreras det JSON-svar som returneras av Visuellt innehåll för en bild som innehåller ett enda människo ansikte.

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

Nästa exempel visar det JSON-svar som returneras för en bild som innehåller flera mänskliga ansikten.

![Syn analys av familjens foto ansikte](./Images/family_photo_face.png)

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

Mer information om hur du använder funktionen för ansikts igenkänning finns i referens dokumentationen för att [analysera avbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
