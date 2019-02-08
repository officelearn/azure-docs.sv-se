---
title: Kategorisera bilder - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp för funktionen bild kategorisering av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1967ba60088cae2b946cfcfe1467c2de5aebccdf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879959"
---
# <a name="image-categorization-with-computer-vision"></a>Bild kategorisering med visuellt innehåll

Förutom taggning och beskrivningar returnerar för visuellt innehåll taxonomi-baserade kategorier som definierats i tidigare versioner. Kategorierna är ordnade som en taxonomi med ärftliga överordnade/underordnade hierarkier. Alla kategorier är på engelska. De kan användas fristående eller med vår nya taggning modeller.

## <a name="the-86-category-concept"></a>Konceptet 86-kategori

Baserat på en lista över 86 begrepp som visas i följande diagram, kan en bild kategoriseras sträcker sig från bred till specifika. Läs den fullständiga taxonomin i textformat i [Kategoritaxonomi](category-taxonomy.md).

![grupperade listor över alla kategorier i kategoritaxonomi](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Bild kategorisering exempel

Följande JSON-svar visar vad för visuellt innehåll returnerar när kategorisera exempelbild baserat på dess visuella funktioner.

![En kvinna under taket i en innesluten byggnad](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

I följande tabell visas en typisk bild-uppsättning och den kategori som returneras av visuellt innehåll för varje bild.

| Bild | Kategori |
|-------|----------|
| ![Fyra personer lösenordsbaserat tillsammans som en serie](./Images/family_photo.png) | people_group |
| ![En Hundvalp som sitter i ett grassy fält](./Images/cute_dog.png) | animal_dog |
| ![En person som står på en mountain rock på solnedgång](./Images/mountain_vista.png) | outdoor_mountain |
| ![En stapel med bröd roller på en tabell](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [tagga bilder](concept-tagging-images.md) och [som beskriver avbildningar](concept-describing-images.md).
