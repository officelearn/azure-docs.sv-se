---
title: OCR kognitiv skicklighet
titleSuffix: Azure Cognitive Search
description: Extrahera text från bildfiler med optisk teckenigenkänning (OCR) i en anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bdb510113a8d65ac04b54e77158f46d03cccd9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791929"
---
# <a name="ocr-cognitive-skill"></a>OCR kognitiv skicklighet

**Ocr-färdigheten (Optical Character Recognition)** känner igen utskriven och handskriven text i bildfiler. Den här färdigheten använder maskininlärningsmodellerna som tillhandahålls av [Datorseende](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) i Cognitive Services. **OCR-färdigheten** mappar till följande funktioner:

+ [API:et "OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) används för andra språk än engelska. 
+ För engelska används det nya "Läs"-API:et. ["Read"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api)

**OCR-färdigheten** extraherar text från bildfiler. Filformat som stöds är:

+ . Jpeg
+ . Jpg
+ . Png
+ . Bmp
+ . Gif
+ . Tiff

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| upptäckaorientering | Aktiverar automatisk avdetection av bildorientering. <br/> Giltiga värden: sant / falskt.|
|standardSpråkkod | <p>  Ingångstextens språkkod. Språk som stöds: <br/> zh-Hans (Kinesisksymplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (tjeckiska) <br/>da (danska) <br/>nl (nederländska) <br/>sv (Engelska) <br/>fi (finska)  <br/>fr (franska) <br/>  de (tyska) <br/>el (grekiska) <br/> hu (ungerska) <br/> det (italienska) <br/>  ja (japanska) <br/> ko (koreanska) <br/> nb (norska) <br/>   pl (polska) <br/> pt (portugisiska) <br/>  ru (ryska) <br/>  es (spanska) <br/>  sv (Svenska) <br/>  tr (turkiska) <br/> ar (arabiska) <br/> ro (rumänska) <br/> sr-Cyrl (serbiskaCyrilliska) <br/> sr-Latn (SerbiskaLatin) <br/>  (slovakiska). <br/>  unk (Okänd) <br/><br/> Om språkkoden är ospecificerad eller null ställs språket in på engelska. Om språket uttryckligen är inställt på "unk" identifieras språket automatiskt. </p> |
|lineEnding (radEnding) | Värdet som ska användas mellan varje identifierad rad. Möjliga värden: "Space",'CarriageReturn','LineFeed'.  Standardär "Space" |

Tidigare fanns det en parameter som kallas "textExtractionAlgorithm" för att ange om färdigheten ska extrahera "tryckt" eller "handskriven" text.  Den här parametern är föråldrad och inte längre nödvändig eftersom den senaste Läs API-algoritmen kan extrahera båda typerna av text på en gång.  Om din färdighetsdefinition redan innehåller den här parametern behöver du inte ta bort den, men den kommer inte längre att användas och båda typerna av text extraheras framöver oavsett vad den är inställd på.

## <a name="skill-inputs"></a>Indata för färdighet

| Indatanamn      | Beskrivning                                          |
|---------------|------------------------------------------------------|
| image         | Komplex typ. För närvarande fungerar endast med fältet "/document/normalized_images", som produceras av ```imageAction``` Azure Blob-indexeraren när den är inställd på ett annat värde än ```none```. Mer information finns i [exemplet.](#sample-output)|


## <a name="skill-outputs"></a>Utdata för färdighet
| Utdatanamn     | Beskrivning                   |
|---------------|-------------------------------|
| text          | Oformaterad text extraherad från bilden.   |
| layoutText    | Komplex typ som beskriver den extraherade texten och platsen där texten hittades.|


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

## <a name="sample-text-and-layouttext-output"></a>Exempel på text och layoutTextutdata

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

Ett vanligt användningsfall för Textsammanslagning är möjligheten att sammanfoga textrepresentationen av bilder (text från en OCR-färdighet eller bildtexten för en bild) till innehållsfältet i ett dokument.

I följande exempel skapar kunskaper ett *merged_text* fält. Det här fältet innehåller dokumentets textinnehåll och OCR-text från var och en av bilderna som är inbäddade i dokumentet.

#### <a name="request-body-syntax"></a>Begärandetextsyntax
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
Exemplet ovan kompetens förutsätter att det finns ett fält med normaliserade bilder. Om du vill generera det här fältet ställer du in *imageAction-konfigurationen* i indexerdefinitionen så att *den genererarNormalizedImages* enligt nedan:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Se även
+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [TextMerger-färdighet](cognitive-search-skill-textmerger.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
