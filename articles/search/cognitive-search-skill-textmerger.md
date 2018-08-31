---
title: Text Merge kognitiv sökning färdighet (Azure Search) | Microsoft Docs
description: Sammanfoga text från en uppsättning fält i ett konsoliderade fält. Använd den här kognitiva kunskaper i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: d90a9f8bd32924eef6533e602957aa1704cfdae9
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190481"
---
#    <a name="text-merge-cognitive-skill"></a>Text Merge kognitiva kunskaper

Den **Text sammanfoga** färdighet konsoliderar text från en samling av fält till ett fält. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Färdighet parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| insertPreTag  | Strängen som ska tas med innan varje infogning. Standardvärdet är `" "`. Om du vill utelämna utrymmet Ställ in värdet `""`.  |
| insertPostTag | Strängen som ska ingå efter varje infogning. Standardvärdet är `" "`. Om du vill utelämna utrymmet Ställ in värdet `""`.  |


##  <a name="sample-input"></a>Exempelindata
Ett JSON-dokument som tillhandahåller användbar indata för den här färdighet kan vara följande:

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

## <a name="extended-sample-skillset-definition"></a>Utökade kompetens exempeldefinition

Ett vanligt scenario för att använda Text Merge är att slå samman textrepresentation av bilder (text från en OCR-kunskaper eller rubriken på en avbildning) i fältet content för ett dokument. 

Följande exempel kompetens använder OCR-kunskaper för att extrahera text från bilder som är inbäddade i dokumentet. Därefter skapas en *merged_text* fält som innehåller både ursprungliga och OCRed text från varje avbildning. 

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
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
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
I exemplet ovan förutsätter att det finns ett normaliserat avbildningar fält. För att få normalized avbildningar fältet kan du ange den *imageAction* konfiguration i din indexerarens definition till *generateNormalizedImages* enligt nedan:

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
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
