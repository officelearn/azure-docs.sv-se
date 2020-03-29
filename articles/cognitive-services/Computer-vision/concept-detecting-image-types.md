---
title: Identifiering av bildtyp - Datorseende
titleSuffix: Azure Cognitive Services
description: Begrepp relaterade till identifieringsfunktionen för bildtyp i API:et för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244570"
---
# <a name="detecting-image-types-with-computer-vision"></a>Identifiera bildtyper med datorseende

Med [API:et analysera bild](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) kan datorseende analysera innehållstypen av bilder, vilket anger om en bild är ClipArt eller en linjeritning.

## <a name="detecting-clip-art"></a>Identifiera ClipArt

Datorseende analyserar en bild och betygsätter sannolikheten för att bilden är ClipArt på en skala från 0 till 3, enligt beskrivningen i följande tabell.

| Värde | Betydelse |
|-------|---------|
| 0 | Non-clip-art |
| 1 | Tvetydig |
| 2 | Normal-clip-art |
| 3 | Bra klipp-art |

### <a name="clip-art-detection-examples"></a>Exempel på identifiering av ClipArt

Följande JSON-svar illustrerar vad Datorseende returnerar när sannolikheten för att exempelbilderna är ClipArt-bilder betyg.

![En ClipArt-bild av en bit ost](./Images/cheese_clipart.png)

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

![Ett blått hus och framsidan](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Identifiera linjeritningar

Computer Vision analyserar en bild och returnerar ett booleskt värde som anger om bilden är en linjeritning.

### <a name="line-drawing-detection-examples"></a>Exempel på identifiering av linjeritning

Följande JSON-svar illustrerar vad Datorseende returnerar när du anger om exempelbilderna är linjeritningar.

![En linjeritningsbild av ett lejon](./Images/lion_drawing.png)

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

![En vit blomma med grön bakgrund](./Images/flower.png)

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

## <a name="use-the-api"></a>Använda API:et

Identifieringsfunktionen för avbildningstyp är en del av [API:et för analyseravbildning.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Du kan anropa det här API:et via en inbyggd SDK eller via REST-anrop. Inkludera `ImageType` i frågeparametern **visualFeatures.** Sedan, när du får hela JSON svar, helt enkelt tolka `"imageType"` strängen för innehållet i avsnittet.

* [Snabbstart: Datorseende .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)
