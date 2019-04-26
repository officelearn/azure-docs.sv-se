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
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 257da397e11843ee96e93f7b3e9bc5ada29822cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203353"
---
# <a name="categorize-images-by-subject-matter"></a>Kategorisera bilder av ämnet

Förutom taggar och en beskrivning returnerar för visuellt innehåll taxonomi-baserade kategorier som har identifierats i en bild. Till skillnad från taggar, kategorier är ordnade i en överordnad/underordnad ärftliga hierarki och det finns färre (till skillnad från tusentals taggar 86). Alla kategorinamn som är på engelska. Kategorisering kan göras av sig själv eller tillsammans med den nya taggar-modellen.

## <a name="the-86-category-concept"></a>Konceptet 86-kategori

Visuellt innehåll kan kategorisera en avbildning brett eller mer specifikt med hjälp av listan över kategorier som 86 i följande diagram. Läs den fullständiga taxonomin i textformat i [Kategoritaxonomi](category-taxonomy.md).

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

| Image | Category |
|-------|----------|
| ![Fyra personer lösenordsbaserat tillsammans som en serie](./Images/family_photo.png) | people_group |
| ![En Hundvalp som sitter i ett grassy fält](./Images/cute_dog.png) | animal_dog |
| ![En person som står på en mountain rock på solnedgång](./Images/mountain_vista.png) | outdoor_mountain |
| ![En stapel med bröd roller på en tabell](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [tagga bilder](concept-tagging-images.md) och [som beskriver avbildningar](concept-describing-images.md).
