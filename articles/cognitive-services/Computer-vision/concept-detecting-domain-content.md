---
title: Identifiera domänspecifika innehåll – visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anger en bild kategorisering domän för att returnera mer detaljerad information om en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 66137f01672820584f97273ddca26a66ada781ba
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312560"
---
# <a name="detect-domain-specific-content"></a>Identifiera domänspecifika innehåll

Dessutom stöder till taggning och övergripande kategorisering, visuellt också ytterligare domänspecifika analys med modeller som har tränats på särskilda data. 

Det finns två sätt att använda domänspecifika modeller: fristående (analys av begränsade) eller som en förbättring av funktionen för kategorisering.

### <a name="scoped-analysis"></a>Begränsade analys

Du kan analysera en bild med endast valda domänspecifika-modellen genom att anropa den [modeller /\<modellen\>/analysera](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API. 

Följande är ett exempel på JSON-svaret som returnerades av den **modeller/kändisar/analysera** API för den angivna avbildningen:

![Satya Nadella standing](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Förbättrad kategorisering analys  

Du kan också använda domänspecifika modeller för att komplettera Allmänt bildanalys. Du gör detta som en del av [övergripande kategorisering](concept-categorizing-images.md) genom att ange domänspecifika modeller i den *information* -parametern för den [analysera](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-anrop. 

I det här fallet kallas 86 kategoritaxonomi klassificeraren först. Om någon av de identifierade kategorierna har en matchande domänspecifika modell leds avbildningen genom att modellen samt och resultatet läggs. 

Följande JSON-svar visar hur domänspecifika analys kan ingå som den `detail` nod i en bredare kategorisering analys.

```json
"categories":[  
  {  
    "name":"abstract_",
    "score":0.00390625
  },
  {  
    "name":"people_",
    "score":0.83984375,
    "detail":{  
      "celebrities":[  
        {  
          "name":"Satya Nadella",
          "faceRectangle":{  
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[  
        {  
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Lista domänspecifika modeller

Visuellt stöder för närvarande följande domänspecifika modeller:

| Namn | Beskrivning |
|------|-------------|
| kändisar | Kändisigenkänning stöds för bilder klassificeras i den `people_` kategori |
| Landmärken | Igenkänning av landmärken, stöds för bilder klassificeras i den `outdoor_` eller `building_` kategorier |

Anropa den [modeller](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API: et returnerar den här informationen tillsammans med de kategorier som varje modell kan gälla:

```json
{  
  "models":[  
    {  
      "name":"celebrities",
      "categories":[  
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {  
      "name":"landmarks",
      "categories":[  
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [kategorisera bilder](concept-categorizing-images.md).
