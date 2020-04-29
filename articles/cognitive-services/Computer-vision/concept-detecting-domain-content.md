---
title: Domänbaserad innehålls-Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anger en avbildnings kategoriserings domän för att returnera mer detaljerad information om en avbildning.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "68946326"
---
# <a name="detect-domain-specific-content"></a>Identifiera domänspecifikt innehåll

Förutom taggning och övergripande kategorisering, stöder Visuellt innehåll också ytterligare domänbaserad analys med hjälp av modeller som har tränats på specialiserade data.

Det finns två sätt att använda domänbaserade modeller: själva (begränsad analys) eller som en förbättring av kategoriserings funktionen.

### <a name="scoped-analysis"></a>Omfattnings analys

Du kan analysera en avbildning med hjälp av den valda domän bara modellen genom att anropa [/Analyze-\<API\>: et för modeller/modell](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) .

Följande är ett exempel på JSON-svar som returneras av **modellerna Models/kändisar/analysera** API för den aktuella avbildningen:

![Satya Nadella position, leende](./images/satya.jpeg)

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

### <a name="enhanced-categorization-analysis"></a>Förbättrad kategoriserings analys

Du kan också använda domänbaserade modeller för att komplettera allmänna bild analyser. Du gör detta som en del av en [övergripande kategorisering](concept-categorizing-images.md) genom att ange företagsspecifika modeller i *informations* parametern i API-anropet [analysera](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .

I det här fallet anropas klassificeraren för 86-kategorin först. Om någon av de identifierade kategorierna har en matchande domänbaserad modell skickas avbildningen även genom den modellen och resultaten läggs till.

Följande JSON-svar visar hur domänbaserad analys kan tas med i `detail` noden i en bredare kategoriserings analys.

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

## <a name="list-the-domain-specific-models"></a>Visa en lista över domänbaserade modeller

Visuellt innehåll stöder för närvarande följande företagsspecifika modeller:

| Name | Beskrivning |
|------|-------------|
| kändisar | Kändis-igenkänning, stöds för bilder som klassificeras i `people_` kategorin |
| landmärken | Landmärkes igenkänning, stöds för bilder som klassificeras i kategorierna `outdoor_` eller `building_` |

Anrop till [modell](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) -API: et returnerar den här informationen tillsammans med de kategorier som varje modell kan använda:

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

Lär dig begrepp om att [kategorisera bilder](concept-categorizing-images.md).
