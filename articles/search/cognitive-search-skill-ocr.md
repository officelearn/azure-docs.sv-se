---
title: OCR kognitiv sökning färdigheter – Azure Search
description: Extrahera text från bildfiler som med optisk teckenigenkänning (OCR) i ett Azure Search berikande pipeline.
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
ms.custom: seodec2018
ms.openlocfilehash: b07c71a9365fca3a2e5d7c837acf689af980afdd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075829"
---
# <a name="ocr-cognitive-skill"></a>OCR kognitiva kunskaper

Den **OCR** färdighet extraherar text från bildfiler. Filformat som stöds är:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . GIF

> [!NOTE]
> Från och med 21 december 2018 kan du koppla en Cognitive Services-resurs med en färdighet i Azure Search. Detta gör det möjligt för oss att börja debitera för körning av färdigheter. Samma datum börjar vi också debitera bildextrahering som en del av dokumentknäckningsfasen. Textextrahering från dokument kommer fortfarande att kunna användas utan kostnad.
>
> Körningen av inbyggda funktioner faktureras till det befintliga [betala per användning-priset för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättningen för bildextrahering följer prissättningen för förhandsversionen. Mer information finns på [prissättningssidan för Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Läs [mer](cognitive-search-attach-cognitive-services.md).
>
>  OCR färdighet mappar till följande cognitive services-funktioner: När textExtractionAlgorithm anges till ”handskriven”, den [”RecognizeText”](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) funktioner används.
>  När textExtractionAlgorithm anges till ”skriva ut”, den [”OCR”](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) funktion används för andra språk än engelska. För engelska, den nya [”identifiera Text”](../cognitive-services/computer-vision/concept-recognizing-text.md) funktioner för tryckt text används.

## <a name="skill-parameters"></a>Färdighet parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| detectOrientation | Aktiverar automatisk igenkänning av bildorientering. <br/> Giltiga värden: true / false.|
|defaultLanguageCode | <p>  Språkkod för den inmatade texten. Språk som stöds: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>CS (Tjeckiska) <br/>da (danska) <br/>NL (nederländska) <br/>en (på engelska) <br/>Fi (finska)  <br/>fr (franska) <br/>  Tyskland (tyska) <br/>el (grekiska) <br/> HU (ungerska) <br/> den (italienska) <br/>  Ja (japanska) <br/> Ko (koreanska) <br/> NB (norska) <br/>   PL (polska) <br/> PT (brasiliansk) <br/>  RU (ryska) <br/>  ES (spanska) <br/>  SA (svenska) <br/>  TR (turkiska) <br/> Kundreskontra (arabiska) <br/> ro (rumänska) <br/> SR-Cyrl (SerbianCyrillic) <br/> SR-Latn (SerbianLatin) <br/>  Sk (slovakiska). <br/>  UNK (okänd) <br/><br/> Om språkkoden är Ospecificerad eller null, ställs språket till engelska. Om språket är explicit inställd på ”unk”, blir språket upptäcks automatiskt. </p> |
| textExtractionAlgorithm | ”ut” eller ”handskriven”. Algoritmen ”handskriven” text igenkänning av OCR förhandsvisas just nu och stöds endast på engelska. |

## <a name="skill-inputs"></a>Färdighet indata

| Indatanamnet      | Beskrivning                                          |
|---------------|------------------------------------------------------|
| image         | Komplexa typen. För närvarande bara fungerar med ”/ dokument/normalized_images” fältet genereras av Azure Blob-indexeraren när ```imageAction``` är inställd på ```generateNormalizedImages```. Se den [exempel](#sample-output) för mer information.|


## <a name="skill-outputs"></a>Färdighet utdata
| Namn på utdata     | Beskrivning                   |
|---------------|-------------------------------|
| text          | Oformaterad text som extraherats från avbildningen.   |
| layoutText    | Komplex typ som beskriver den extrahera texten samt den plats där texten hittades.|


## <a name="sample-definition"></a>Exempeldefinition

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

## <a name="sample-text-and-layouttext-output"></a>Exempel på text och layoutText utdata

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exempel: Sammanslagning av text som extraherats från inbäddade bilder med innehållet i dokumentet.

Ett vanligt användningsfall för Text fusion är möjligheten att slå samman textrepresentation av bilder (text från en OCR-kunskaper eller rubriken på en avbildning) i fältet content för ett dokument. 

Följande exempel kompetens skapar en *merged_text* fält som innehåller det faktiska innehållet i dokumentet, samt OCRed texten från var och en av avbildningarna som är inbäddad i detta dokument. 

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
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Exemplet ovan kompetens förutsätter att det finns ett normaliserat avbildningar fält. Om du vill generera det här fältet, ange den *imageAction* konfiguration i din indexerarens definition till *generateNormalizedImages* enligt nedan:

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
+ [TextMerger färdighet](cognitive-search-skill-textmerger.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
