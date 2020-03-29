---
title: Domänspecifikt innehåll - Datorseende
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anger en bildkategoriseringsdomän för att returnera mer detaljerad information om en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68946326"
---
# <a name="detect-domain-specific-content"></a>Identifiera domänspecifikt innehåll

Förutom taggning och kategorisering på hög nivå stöder Computer Vision också ytterligare domänspecifik analys med hjälp av modeller som har utbildats på specialiserade data.

Det finns två sätt att använda de domänspecifika modellerna: av sig själva (begränsad analys) eller som en förbättring av kategoriseringsfunktionen.

### <a name="scoped-analysis"></a>Analys av omfattning

Du kan analysera en avbildning med endast den valda domänspecifika modellen genom att anropa [modell/\<\>modell /Analysera](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API.

Följande är ett exempel JSON svar returneras av **modeller / kändisar / analysera** API för den givna bilden:

![Satya Nadella stående, leende](./images/satya.jpeg)

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

### <a name="enhanced-categorization-analysis"></a>Förbättrad kategoriseringsanalys

Du kan också använda domänspecifika modeller för att komplettera allmän bildanalys. Du gör detta som en del av [högnivåkategorisering](concept-categorizing-images.md) genom att ange domänspecifika modeller i *parametern* [Analysera](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API i analys-API-anropet.

I det här fallet kallas 86-kategori taxonomin klassificeraren först. Om någon av de identifierade kategorierna har en matchande domänspecifik modell skickas avbildningen också genom den modellen och resultaten läggs till.

Följande JSON-svar visar hur domänspecifik analys `detail` kan inkluderas som noden i en bredare kategoriseringsanalys.

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

## <a name="list-the-domain-specific-models"></a>Lista de domänspecifika modellerna

För närvarande stöder Datorseende följande domänspecifika modeller:

| Namn | Beskrivning |
|------|-------------|
| Kändisar | Kändisigenkänning, stöds `people_` för bilder som klassificeras i kategorin |
| Landmärken | Landmark erkännande, stöds för `outdoor_` bilder `building_` som klassificeras i eller kategorier |

Om du [anropar](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) modell-API:et returneras den här informationen tillsammans med de kategorier som varje modell kan tillämpas på:

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

Lär dig begrepp om [att kategorisera bilder](concept-categorizing-images.md).
