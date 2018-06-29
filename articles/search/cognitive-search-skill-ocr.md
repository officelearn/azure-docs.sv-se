---
title: OCR kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Extrahera text från bildfiler i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 478afe81ed739b98487973eb092ee9cad0aa17fd
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059195"
---
# <a name="ocr-cognitive-skill"></a>OCR kognitiva kunskaper

Den **OCR** kunskaper hämtar text från bildfiler. Filformat som stöds är:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . GIF


## <a name="skill-parameters"></a>Kunskaper parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| detectOrientation | Aktiverar automatisk igenkänning av orientering. <br/> Giltiga värden: true / false.|
|defaultLanguageCode | <p>  Språkkod av indatatexten. Språk som stöds: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>CS (Tjeckien) <br/>da (danska) <br/>NL (holländska) <br/>SV (på engelska) <br/>Fi (finska)  <br/>fr (franska) <br/>  Tyskland (tyska) <br/>el (grekiska) <br/> HU (ungerska) <br/> den (italienska) <br/>  Ja (japanska) <br/> Ko (koreanska) <br/> NB (norska) <br/>   PL (polska) <br/> PT (brasiliansk) <br/>  RU (ryska) <br/>  ES (spanska) <br/>  SA (svenska) <br/>  TR (Turkisk) <br/> ar (arabiska) <br/> ro (rumänska) <br/> SR-Cyrl (SerbianCyrillic) <br/> SR-Latn (SerbianLatin) <br/>  Sk (slovakiska). <br/>  UNK (okänd) <br/><br/> Om koden är Ospecificerad eller null, är språket som autodetected. </p> |
| textExtractionAlgorithm | ”ut” eller ”handskriven”. Algoritmen ”handskriven” text recognition OCR är för närvarande under förhandsgranskning och stöds endast på engelska. |

## <a name="skill-inputs"></a>Kunskaper indata

| Indatanamnet      | Beskrivning                                          |
|---------------|------------------------------------------------------|
| image         | Komplex typ. För närvarande bara fungerar med ”/ dokument/normalized_images” fältet produceras av Azure Blob-indexeraren när ```imageAction``` är inställd på ```generateNormalizedImages```. Finns det [exempel](#sample-output) för mer information.|


## <a name="skill-outputs"></a>Kunskaper utdata
| Namnet på målet     | Beskrivning                   |
|---------------|-------------------------------|
| text          | Klartext extraheras från avbildningen.   |
| layoutText    | Komplex typ som beskriver den extraherade texten samt den plats där texten hittades.|


## <a name="sample-definition"></a>Exempel definition

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Exempel på utdata från text och layoutText

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exempel: Sammanslagning text som hämtats från inbäddade bilder med innehållet i dokumentet.

Ett vanligt användningsfall för Text fusion är möjligheten att koppla en textrepresentation av avbildningar (text från en OCR kunskap eller en titel på en bild) i fältet content för ett dokument. 

Följande exempel kunskaper skapar en *merged_text* fältet ska innehålla textinnehåll dokumentet, samt OCRed text från var och en av avbildningarna som är inbäddad i detta dokument. 

#### <a name="request-body-syntax"></a>Begärandetextsyntax
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
Kunskaper i exemplet ovan förutsätter att det finns ett normaliserat bilder fält. Om du vill generera det här fältet anger den *imageAction* konfigurationen i indexeraren-definitionen så att *generateNormalizedImages* enligt nedan:

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
+ [TextMerger kunskaper](cognitive-search-skill-textmerger.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
