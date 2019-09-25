---
title: Text sammanfogning kognitiv Sök kompetens – Azure Search
description: Sammanfoga text från en samling fält till ett konsoliderat fält. Använd den här kognitiva kunskapen i en Azure Search anriknings pipeline.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 1e88fcc13d97d92cf9b35616ecb7d71c2d24db1f
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265263"
---
#    <a name="text-merge-cognitive-skill"></a>Text sammanfogning av kognitiva kunskaper

**Text sammanfognings** kunskapen konsoliderar text från en samling fält till ett enda fält. 

> [!NOTE]
> Den här kunskapen är inte kopplad till ett Cognitive Services-API och du debiteras inte för att använda den. Du bör fortfarande [bifoga en Cognitive Services resurs](cognitive-search-attach-cognitive-services.md), men för att åsidosätta det **kostnads fria** resurs alternativet som begränsar dig till ett litet antal dagliga berikare per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| insertPreTag  | Sträng som ska tas med före varje infogning. Standardvärdet är `" "`. Om du vill utelämna utrymmet ställer du in värdet `""`på.  |
| insertPostTag | Sträng som ska tas med efter varje infogning. Standardvärdet är `" "`. Om du vill utelämna utrymmet ställer du in värdet `""`på.  |


##  <a name="sample-input"></a>Exempel på inmatade
Ett JSON-dokument som ger användbar indatamängd för den här kunskapen kan vara:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28],
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Exempel på utdata
I det här exemplet visas resultatet av föregående indata, förutsatt att *insertPreTag* är inställt `" "`på, och *insertPostTag* är inställt på `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Utökad exempel på färdigheter-definition

Ett vanligt scenario för att använda text sammanfogning är att slå samman text representationen av bilder (text från en OCR-kunskap eller bild text) i innehålls fältet i ett dokument. 

I följande exempel färdigheter används OCR-kunskaper för att extrahera text från bilder som är inbäddade i dokumentet. Därefter skapas ett *merged_text* -fält som innehåller både original-och OCRed text från varje bild. Du kan lära dig mer om OCR-kunskaper [här](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr).

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
Exemplet ovan förutsätter att det finns ett normaliserat avbildnings fält. För att hämta normaliserade-images-fält anger du *imageAction* -konfigurationen i din Indexer-definition till *generateNormalizedImages* enligt nedan:

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
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
