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
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a85fb65ad971bfc70bdab0689e99921e616b56ac
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308867"
---
# <a name="categorize-images"></a>Kategorisera bilder

Förutom taggar och en beskrivning returnerar för visuellt innehåll taxonomi-baserade kategorier som har identifierats i en bild. Till skillnad från taggar, kategorier är ordnade i en överordnad/underordnad ärftliga hierarki och det finns färre (till skillnad från tusentals taggar 86). Alla kategorinamn som är på engelska. Kategorisering kan göras av sig själv eller tillsammans med den nya taggar-modellen.

## <a name="the-86-category-concept"></a>Konceptet 86-kategori

Visuellt innehåll kan kategorisera en avbildning brett eller mer specifikt med hjälp av listan över 86 andra i följande diagram. Läs den fullständiga taxonomin i textformat i [Kategoritaxonomi](category-taxonomy.md).

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
