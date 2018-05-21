---
title: Text Merge kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Sammanfoga text från en uppsättning fält i en konsoliderad fältet. Använd kognitiva kompetensen i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: f349158873acca9d50d4d6e5fdfa3539f26207fe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
#    <a name="text-merge-cognitive-skill"></a>Text Merge kognitiva kunskaper

Den **Text sammanfoga** kunskaper konsoliderar text från en uppsättning fält i ett enda fält. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.TextMerger

## <a name="skill-parameters"></a>Kunskaper parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| insertPreTag  | Strängen som ska tas med innan varje infogning. Standardvärdet är `" "`. Om du vill utelämna området, anger du värdet till `""`.  |
| insertPostTag | Strängen som ska tas med när varje infogning. Standardvärdet är `" "`. Om du vill utelämna området, anger du värdet till `""`.  |


##  <a name="sample-input"></a>Exempelindata
En JSON-dokumentet som tillhandahåller användbar indata för kompetensen kan vara:

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Exempel på utdata
Det här exemplet visar utdata från föregående indata, under förutsättning att den *insertPreTag* är inställd på `" "`, och *insertPostTag* är inställd på `""`. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "mergedText": "The quick brown fox jumps over the lazy dog" 
           }
      }
    ]
}
```

## <a name="extended-sample-skillset-definition"></a>Utökade exempel kunskaper definition

Ett vanligt scenario för med Text är att koppla en textrepresentation av avbildningar (text från en OCR kunskap eller en titel på en bild) i fältet content för ett dokument. 

Följande exempel kunskaper använder OCR kunskaper för att extrahera text från bilder som är inbäddade i dokumentet. Därefter skapar den en *merged_text* fältet ska innehålla både ursprungliga och OCRed text från varje avbildning. 

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Util.TextMerger",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " "
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
Exemplet ovan förutsätter att det finns ett normaliserat bilder fält. För att få normaliserade bilder fältet, ange den *imageAction* konfigurationen i indexeraren-definitionen så att *generateNormalizedImages* enligt nedan:

```json
{  
   //...rest of your indexer definition goes here ... 
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](ref-create-indexer.md)