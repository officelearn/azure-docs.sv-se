---
title: Kognitiv färdighet för textkoppling
titleSuffix: Azure Cognitive Search
description: Sammanfoga text från en grupp fält till ett konsoliderat fält. Använd den här kognitiva färdigheten i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98ea416305f080850d85498f74693eb2d45b0944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162352"
---
#   <a name="text-merge-cognitive-skill"></a>Kognitiv färdighet för textkoppling

Med **textkoppling** konsolideras text från en samling fält till ett enda fält. 

> [!NOTE]
> Den här färdigheten är inte bunden till ett Cognitive Services API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md)för att åsidosätta alternativet **Kostnadsfri** resurs som begränsar dig till ett litet antal dagliga berikningar per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.mergeSkill

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| insertPreTag  | Sträng som ska inkluderas före varje insättning. Standardvärdet är `" "`. Om du vill utelämna utrymmet `""`ställer du in värdet på .  |
| insertPostTag | Sträng som ska inkluderas efter varje insättning. Standardvärdet är `" "`. Om du vill utelämna utrymmet `""`ställer du in värdet på .  |


##  <a name="sample-input"></a>Exempelinmatning
Ett JSON-dokument som ger användbara indata för den här färdigheten kan vara:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Exempel på utdata
I det här exemplet visas utdata för föregående indata, `" "`förutsatt att *insertPreTag* är inställt på , och *insertPostTag* är inställt på `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Utökad definition av exempelkompetens

Ett vanligt scenario för att använda Textkoppling är att koppla textrepresentationen av bilder (text från en OCR-färdighet eller bildtexten för en bild) till innehållsfältet i ett dokument. 

I följande exempel använder kunskaper ocr-färdigheten OCR för att extrahera text från bilder som är inbäddade i dokumentet. Därefter skapas ett *merged_text* fält som innehåller både original- och OCR-text från varje bild. Du kan läsa mer om OCR skicklighet [här](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr).

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
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
      "insertPostTag": " ",
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
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Exemplet ovan förutsätter att det finns ett fält med normaliserade bilder. Om du vill få fältet normaliserade bilder ställer du in *imageAction-konfigurationen* i indexeringsdefinitionen så att *den genererarNormaliserade bilder* enligt nedan:

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

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
