---
title: Identifiera ansikten - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för att identifiera ansikten med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: df1cd989d82fa5541c2d81fe6629671d6e063bc4
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984562"
---
# <a name="detecting-faces"></a>Identifiera ansikten

Visuellt identifierar ansikten i bilder och genererar den ålder, kön och rektangel för varje identifierad ansikte. Visuellt innehåll tillhandahåller en delmängd av de funktioner som finns i [Ansiktsigenkänning](/azure/cognitive-services/face/) och du kan använda tjänsten för ansiktsigenkänning för mer detaljerad analys, till exempel identifiering av ansikten och posering.  

## <a name="face-detection-examples"></a>Exempel för identifiering av ansikte

Det första exemplet visar JSON-svaret som returnerades av visuellt innehåll för en avbildning som innehåller ett enda mänskliga ansikte.

![Visuellt innehåll analyserar kvinna tak ansikte](./Images/woman_roof_face.png)

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

Det andra exemplet visar den JSON-svar returneras för en avbildning som innehåller flera ansikten.

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

Lär dig begrepp [identifierar domänspecifika innehåll](concept-detecting-domain-content.md).