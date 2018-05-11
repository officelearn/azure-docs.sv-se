---
title: Bearbeta och extrahera text från avbildningar i Azure Search | Microsoft Docs
description: Processen och extrahera text och annan information från bilder i kognitiva söka pipelines i Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: c58e731f6b8c86a0b7d6f2500d81077904b2f5ef
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Hur du extrahera informationen från bilder i kognitiva Sök scenarier

Kognitiva sökning har flera funktioner för att arbeta med bilder och bildfiler. Under dokument knäcka, kan du använda den *imageAction* parametern för att extrahera text från foton och bilder som innehåller alfanumeriska text, till exempel ordet ”STOP” i ett stop-tecken. Andra scenarier som inkluderar genererar en textrepresentation av en avbildning, till exempel ”dandelion” för ett foto av ett dandelion eller färgen ”gult”. Du kan också extrahera metadata om avbildningen, till exempel dess storlek.

Den här artikeln täcker bildbearbetning i detalj och innehåller anvisningar för att arbeta med bilder i en kognitiva Sök-pipeline.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Hämta normaliserade bilder

Som en del av dokumentet knäcka finns det en ny uppsättning indexeraren konfigurationsparametrar för hantering av bildfiler eller bilder som är inbäddade i filer. Dessa parametrar används för att normalisera avbildningar för ytterligare nedströms bearbetning. Normaliserar avbildningar gör dem mer enhetlig. Stora bilder ändras till en maximal höjd och bredd så att de kan användas. För att tillhandahålla metadata på riktning, justeras avbildningen rotation för lodrät lästes in. Justering av metadata fångas i en komplex typ som skapas för varje avbildning. 

Du kan inte inaktivera avbildningen normalisering. Kunskaper som itererar över avbildningar förvänta normaliserade bilder.

| Konfigurationsparameter | Beskrivning |
|--------------------|-------------|
| imageAction   | Värdet ”none” om ingen åtgärd som ska vidtas när inbäddade bilder eller bildfiler påträffades. <br/>Ange till ”generateNormalizedImages” för att generera en matris med normaliserade avbildningar som en del av dokumentet sprickbildning. Dessa avbildningar visas i den *normalized_images* fältet. <br/>Standardvärdet är ”none”. Den här konfigurationen är endast relevant för blob-datakällor när ”dataToExtract” anges till ”contentAndMetadata”. |
|  normalizedImageMaxWidth | Den maximala bredden (i bildpunkter) för normaliserade bilder som skapas. Standardvärdet är 2000.|
|  normalizedImageMaxHeight | Maximal höjd (i bildpunkter) för normaliserade bilder som skapas. Standardvärdet är 2000.|

> [!NOTE]
> Om du ställer in den *imageAction* -egenskapen till något annat än ”none” du kommer inte att ange den *parsingMode* egenskapen till något annat än ”standard”.  Du kan bara ange en av de här två egenskaperna till ett standardvärde i indexeraren konfigurationen.

2000 bildpunkter normaliserade bilder maximala bredd och höjd Standardvärdet baseras på de maximala storlekar som stöds av den [OCR kunskaper](cognitive-search-skill-ocr.md) och [bild analys kunskaper](cognitive-search-skill-image-analysis.md). Om du ökar de maximala gränserna, misslyckas bearbetning på stora bilder.


Du anger imageAction i din [indexeraren definition](ref-create-indexer.md) på följande sätt:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

När den *imageAction* är inställd på ”generateNormalizedImages” den nya *normalized_images* innehåller en matris med bilder. Varje avbildning är en komplex typ som har följande medlemmar:

| Bild medlem       | Beskrivning                             |
|--------------------|-----------------------------------------|
| data               | Base64-kodad sträng normaliserade bilden i JPEG-format.   |
| Bredd              | Bredden på normaliserad bilden i bildpunkter. |
| Höjd             | Höjden på normaliserad bilden i bildpunkter. |
| originalWidth      | Den ursprungliga bredden på avbildningen innan normalisering. |
| originalHeight      | Den ursprungliga höjden på avbildningen innan normalisering. |
| rotationFromOriginal |  Motsols rotation i grader som inträffade för att skapa normaliserade avbildningen. Ett värde mellan 0 grader och 360 grader. Det här steget läser metadata från den avbildning som genereras av en kamera eller skanner. Vanligtvis en multipel av 90 grader. |
| contentOffset |Teckenförskjutningen i fältet content där avbildningen har extraherats från. Det här fältet används endast för filer med inbäddade bilder. |

 Exempel på värdet för *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>Bild-relaterade kunskaper

Det finns två inbyggda kognitiva kunskaper som ta bilder som indata: [OCR](cognitive-search-skill-ocr.md) och [avbildningen Analysis](cognitive-search-skill-image-analysis.md). 

Dessa kunskaper fungerar för närvarande endast med bilder som skapas från dokumentet knäcka steg. Därför stöds endast indata är `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Bild analys kunskaper

Den [avbildningen analys kunskaper](cognitive-search-skill-image-analysis.md) extraherar en omfattande uppsättning visuella funktioner baserat på innehållet i operativsystemsavbildningen. Till exempel generera en rubrik från en avbildning, generera taggar eller identifiera Kändisars och landmärken.

### <a name="ocr-skill"></a>OCR kunskaper

Den [OCR kunskaper](cognitive-search-skill-ocr.md) hämtar text från bildfiler som JPG, PNG-filer och bitmappar. Det kan extrahera text samt layoutinformation. Layoutinformationen innehåller omgivande rutorna för alla strängar som identifieras.

OCR kompetensen kan du välja algoritmen som ska användas för identifiering av text i bilder. För närvarande stöder två algoritmer, en för tryckt text och ett annat för handskriven text.

## <a name="embedded-image-scenario"></a>Scenario för inbäddad bild

Ett vanligt scenario omfattar att skapa en sträng som innehåller alla filinnehållet, både text och bildens ursprungspunkt text, genom att utföra följande steg:  

1. [Extrahera normalized_images](#get-normalized-images)
1. Kör OCR kunskaper med `"/document/normalized_images"` som indata
1. Sammanfoga Textrepresentationen av dessa avbildningar med oformaterade texten som extraheras från filen. Du kan använda den [Text sammanfoga](cognitive-search-skill-textmerger.md) kunskaper för att konsolidera både textsegment till en enda stor.

Följande exempel kunskaper skapar en *merged_text* fält som innehåller det faktiska innehållet i dokumentet. Den omfattar också OCRed text från var och en av de inbäddade bilderna. 

#### <a name="request-body-syntax"></a>Begäran brödtext syntax
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

Nu när du har ett merged_text-fält kan du mappa den som sökbara fält i indexeraren-definition. Alla av innehållet i filerna, inklusive texten för bilder, blir sökbar.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualisera avgränsar rutor extraherade text

Ett annat vanligt scenario är att visualisera information om sökresultat layout. Du kanske vill markera var en del av texten finns i en avbildning som en del av sökresultatet.

Eftersom OCR steg utförs på normaliserad bilder, är layout koordinaterna i området normaliserade avbildningen. När normaliserade bilden visas, förekomst av koordinater är vanligtvis inte ett problem, men i vissa fall kanske du vill visa den ursprungliga avbildningen. Konvertera varje koordinaten punkter i layouten i detta fall till det ursprungliga avbildningen koordinatsystemet. 

Om du vill omvandla normaliserade koordinater till den ursprungliga koordinatsystem som en hjälp kan du använda följande algoritm:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Se också
+ [Skapa indexerare (REST)](ref-create-indexer.md)
+ [Analysera avbildningen kunskaper](cognitive-search-skill-image-analysis.md)
+ [OCR kunskaper](cognitive-search-skill-ocr.md)
+ [Text merge kunskaper](cognitive-search-skill-textmerger.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Utökade mappning](cognitive-search-output-field-mapping.md)
