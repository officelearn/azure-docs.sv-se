---
title: Kategorisera bilder - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för att kategorisera bilder med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 7062d98d40c15f4e9e873038fc12fc1b104c996d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333487"
---
# <a name="categorizing-images"></a>Kategorisera bilder

Förutom taggning och beskrivningar returnerar för visuellt innehåll taxonomi-baserade kategorier som definierats i tidigare versioner. Kategorierna är ordnade som en taxonomi med ärftliga överordnade/underordnade hierarkier. Alla kategorier är på engelska. De kan användas fristående eller med vår nya taggning modeller.

## <a name="the-86-category-concept"></a>Konceptet 86-kategori

Baserat på en lista över 86 begrepp som visas i följande diagram, kan en bild kategoriseras sträcker sig från bred till specifika. Läs den fullständiga taxonomin i textformat i [Kategoritaxonomi](category-taxonomy.md).

![grupperade listor över alla kategorier i kategoritaxonomi](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Bild kategorisering exempel

Följande JSON-svar visar vad för visuellt innehåll returnerar när kategorisera exempelbild baserat på dess visuella funktioner.

![Kvinna tak](./Images/woman_roof.png)

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
| ![Familjefoto](./Images/family_photo.png) | people_group |
| ![Gullig hund](./Images/cute_dog.png) | animal_dog |
| ![Berg utomhus](./Images/mountain_vista.png) | outdoor_mountain |
| ![Visuell analys mat bröd](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [tagga bilder](concept-tagging-images.md) och [som beskriver avbildningar](concept-describing-images.md).