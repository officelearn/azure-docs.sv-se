---
title: Identifiera bildtyper - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för att identifiera bildtyper som använder den API för visuellt innehåll.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 1a76106caed514f56e897203fba5215b7e93d4ff
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984834"
---
# <a name="detecting-image-types"></a>Identifiera bildtyper

Visuellt innehåll kan analysera innehållstyp avbildningar genom att ange om en bild är ClipArt, omdöme sannolikheten på en skala eller en Linjeteckning.

## <a name="detecting-clip-art"></a>Identifiera ClipArt

Visuellt innehåll analyserar en bild och bedömer risken för avbildningen ClipArt på en skala från 0 till 3, enligt beskrivningen i följande tabell.

| Värde | Betydelse |
|-------|---------|
| 0 | Icke-ClipArt |
| 1 | tvetydig |
| 2 | Normal ClipArt |
| 3 | bra ClipArt |

### <a name="clip-art-detection-examples"></a>Clip art identifiering exempel

Följande JSON-svar visar vad för visuellt innehåll returnerar vid bedömning av risken för exempel-avbildningar som ClipArt.

![Visuellt innehåll analyserar ost ClipArt](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Visuellt innehåll analyserar House Yard](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Identifiera rad ritningar

Visuellt innehåll analyserar en bild och returnerar ett booleskt värde som anger om bilden är en Linjeteckning.

### <a name="line-drawing-detection-examples"></a>Linjeteckning identifiering exempel

Följande JSON-svar visar vad för visuellt innehåll returnerar när som anger om exempel bilderna är rad ritningar.

![Visuellt innehåll analyserar Lion ritning](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Visuellt innehåll analyserar blommor](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [tagga bilder](concept-tagging-images.md) och [kategorisera bilder](concept-categorizing-images.md).