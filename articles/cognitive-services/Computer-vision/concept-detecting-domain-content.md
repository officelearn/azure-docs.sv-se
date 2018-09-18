---
title: Identifiera domänspecifika innehåll – visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för som beskriver bilder med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: b197227b6550703e4cc303fc800839dc48bf2d1c
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985217"
---
# <a name="detecting-domain-specific-content"></a>Identifiera domänspecifikt innehåll

Dessutom stöder också till taggning och översta kategorisering, visuellt specialiserad (eller domänspecifika) information. Särskild information kan implementeras som en fristående metod eller med den övergripande kategoriseringen. Den fungerar som ett sätt att ytterligare förfina 86 kategoritaxonomi genom tillägg av domänspecifika modeller.

Det finns två alternativ för att använda domänspecifika modeller:

* Begränsade analys  
  Analysera bara en vald modell genom att aktivera en HTTP POST-anrop. Om du vet vilken modell som du vill använda kan du ange modellens namn. Du bara hämta information relevanta för den modellen. Du kan till exempel använda det här alternativet för att bara söka efter kändisigenkänning. Svaret innehåller en lista över potential matchar kändisar, tillsammans med sina förtroende poäng.
* Förbättrad analys  
  Analysera för att tillhandahålla ytterligare information som rör kategorier från den 86 kategoritaxonomi. Det här alternativet är tillgängligt för användning i program där användare vill få allmän bildanalys utöver information från en eller flera domänspecifika modeller. När den här metoden har anropats kallas 86 kategoritaxonomi klassificeraren först. Om någon av kategorierna matchar som kända eller matchande modeller följer en igenom av klassificerare anrop. Till exempel om den `details` -parametern för anropet HTTP POST anges antingen till ”alla” eller innehåller ”kändisar”, metoden anropar kändisar klassificeraren när 86-kategori-klassificerare kallas. Om avbildningen har klassificerats som `people_` eller en underkategori i den kategorin och kändis klassificeraren sedan anropas.

## <a name="listing-domain-specific-models"></a>Visa en lista över domänspecifika modeller

Du kan visa de domänspecifika modeller som stöds av visuellt innehåll. Visuellt stöder för närvarande följande domänspecifika modeller för att upptäcka domänspecifika innehåll:

| Namn | Beskrivning |
|------|-------------|
| kändisar | Kändisigenkänning stöds för bilder klassificeras i den `people_` kategori |
| Landmärken | Igenkänning av landmärken, stöds för bilder klassificeras i den `outdoor_` eller `building_` kategorier |

### <a name="domain-model-list-example"></a>Exempel på domänen modellen

Följande JSON-svar visar en lista över de domänspecifika modeller som stöds av visuellt innehåll.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [kategorisera bilder](concept-categorizing-images.md).