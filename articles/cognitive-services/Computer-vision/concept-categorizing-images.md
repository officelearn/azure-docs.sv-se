---
title: Bildkategorisering - Datorseende
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör bildkategoriseringsfunktionen i API:et för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 477349f1addf71a30e8ecb179266d8eac5510887
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244758"
---
# <a name="categorize-images-by-subject-matter"></a>Kategorisera bilder efter ämne

Förutom taggar och en beskrivning returnerar Datorseende de taxonomibaserade kategorier som identifieras i en bild. Till skillnad från taggar är kategorier ordnade i en ärftlig hierarki för överordnad/underordnad och det finns färre av dem (86, i motsats till tusentals taggar). Alla kategorinamn är på engelska. Kategorisering kan göras av sig själv eller tillsammans med den nyare taggar modellen.

## <a name="the-86-category-concept"></a>Konceptet 86-kategori

Datorseende kan kategorisera en bild brett eller specifikt med hjälp av listan över 86 kategorier i följande diagram. Läs den fullständiga taxonomin i textformat i [Kategoritaxonomi](category-taxonomy.md).

![Grupperade listor över alla kategorier i kategorin taxonomi](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Exempel på bildkategorisering

Följande JSON-svar illustrerar vad datorseende returnerar när exempelbilden kategoriseras baserat på dess visuella funktioner.

![En kvinna på taket till ett flerfamiljshus](./Images/woman_roof.png)

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

Följande tabell illustrerar en typisk bilduppsättning och den kategori som returneras av datorseende för varje bild.

| Bild | Kategori |
|-------|----------|
| ![Fyra personer poserade tillsammans som en familj](./Images/family_photo.png) | people_group |
| ![En valp som sitter i ett gräsbevuxet fält](./Images/cute_dog.png) | animal_dog |
| ![En person som står på en bergsten vid solnedgången](./Images/mountain_vista.png) | outdoor_mountain |
| ![En hög med brödroller på ett bord](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Använda API:et

Kategoriseringsfunktionen är en del av [Api:et för analyseravbildning.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Du kan anropa det här API:et via en inbyggd SDK eller via REST-anrop. Inkludera `Categories` i frågeparametern **visualFeatures.** Sedan, när du får hela JSON svar, helt enkelt tolka `"categories"` strängen för innehållet i avsnittet.

* [Snabbstart: Datorseende .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Nästa steg

Lär dig de relaterade begreppen [att tagga bilder](concept-tagging-images.md) och [beskriva bilder](concept-describing-images.md).
