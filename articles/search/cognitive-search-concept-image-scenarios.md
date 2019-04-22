---
title: Processen och extrahera text från bilder i cognitive search – Azure Search
description: Processen och extrahera text och annan information från bilder i cognitive Sök pipelines i Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f1491d6b87816dfc70e94e01653567bda101d045
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916979"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Hur du bearbetar och extrahera information från bilder i scenarier med kognitiv sökning

Kognitiv sökning har flera funktioner för att arbeta med bilder och bildfiler. Under dokumentknäckning, kan du använda den *imageAction* parametern för att extrahera text från bilder eller bilder som innehåller alfanumeriska text, till exempel ordet ”stoppa” i ett stop-tecken. Andra scenarier är genererar en textmotsvarighet till en avbildning, till exempel ”dandelion” för ett foto av ett dandelion eller färgen ”gul”. Du kan också extrahera metadata om bilden, till exempel dess storlek.

Den här artikeln beskriver bildbearbetning i detalj och innehåller anvisningar för att arbeta med bilder i en pipeline för kognitiv sökning.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Hämta normaliserade bilder

Som en del av dokumentknäckning finns det en ny uppsättning konfigurationsparametrar för indexeraren för att hantera bildfiler eller bilder som är inbäddade i filer. Dessa parametrar används för att normalisera avbildningar för ytterligare nedströms bearbetning. Normaliserar avbildningar gör dem mer enhetlig. Stora bilder ändras till en maximal höjd och bredd så att de ska använda. För avbildningar med metadata om orientering kan justeras Bildrotation för lodrät läses in. Justering av metadata som avbildas i en komplex typ som skapas för varje bild. 

Du kan inte inaktivera normalisering av avbildningen. Som en itererar över avbildningar förväntar sig normaliserade avbildningar.

| Konfigurationsparameter | Beskrivning |
|--------------------|-------------|
| imageAction   | Ange ”none” om ingen åtgärd ska vidtas när inbäddade bilder eller bildfiler uppstår. <br/>Ange till ”generateNormalizedImages” för att generera en matris med normaliserad avbildningar som en del av document cracking.<br/>Ange till ”generateNormalizedImagePerPage” att generera en matris med normaliserad avbildningar för PDF-filer i din datakälla, återges där varje sida som en utdata-bild.  Funktionen är samma som ”generateNormalizedImages” för icke-PDF-filtyper.<br/>För alla alternativ som inte är ”none” bilderna visas i den *normalized_images* fält. <br/>Standardvärdet är ”ingen”. Den här konfigurationen är bara relevant till blob-datakällor, när ”dataToExtract” är inställt på ”contentAndMetadata”. <br/>Högst 1000 avbildningar kommer att extraheras från ett visst dokument. Om det finns fler än 1000 bilder i ett dokument, första 1000 kommer att extraheras och kommer att genereras en varning. |
|  normalizedImageMaxWidth | Den maximala bredden (i bildpunkter) för normaliserade bilder som skapas. Standardvärdet är 2000.|
|  normalizedImageMaxHeight | Maximal höjd (i bildpunkter) för normaliserade bilder som skapas. Standardvärdet är 2000.|

> [!NOTE]
> Om du ställer in den *imageAction* egenskapen till något annat än ”none”, du kommer inte att kunna ställa in den *parsingMode* egenskapen till något annat än ”standard”.  Du kan bara ange en av dessa två egenskaper till ett icke-standard-värde i konfigurationen indexerare.

Ange den **parsingMode** parameter `json` (för att indexera varje blob som ett enda dokument) eller `jsonArray` (om din blobbarna innehåller JSON-matriser och du behöver varje element i en matris ska betraktas som ett separat dokument).

I stället för 2000 bildpunkter för normaliserade avbildningar maximala bredden och höjden baserat på de maximala storlekar som stöds av den [OCR färdighet](cognitive-search-skill-ocr.md) och [bild analysis färdighet](cognitive-search-skill-image-analysis.md). Om du ökar de maximala gränserna, misslyckas bearbetning på större avbildningar.


Du anger imageAction i din [indexerardefinitionen](https://docs.microsoft.com/rest/api/searchservice/create-indexer) på följande sätt:

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

När den *imageAction* anges till ett värde andra sedan ”none” den nya *normalized_images* fältet innehåller en matris med bilder. Varje avbildning är en komplex typ som har följande medlemmar:

| Bild medlem       | Beskrivning                             |
|--------------------|-----------------------------------------|
| data               | Base64-kodad sträng av normaliserade bild i JPEG-format.   |
| Bredd              | Bredden på normaliserad avbildningen i bildpunkter. |
| Höjd             | Höjden på normaliserad avbildningen i bildpunkter. |
| originalWidth      | Den ursprungliga bredden innan normalisering. |
| originalHeight      | Bilden innan normalisering ursprungliga höjd. |
| rotationFromOriginal |  Motsols rotation i grader som inträffat normaliserade avbildning. Ett värde mellan 0 grader och 360 grader. Det här steget läser metadata från den avbildning som genereras av en kamera eller skanner. Vanligtvis en multipel av 90 grader. |
| contentOffset |Förskjutningen tecknet i fältet content där avbildningen har extraherats från. Det här fältet kan bara användas för filer med inbäddade bilder. |

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

Det finns två inbyggda kognitiva kunskaper som tar bilder som indata: [OCR](cognitive-search-skill-ocr.md) och [bild Analysis](cognitive-search-skill-image-analysis.md). 

Dessa kunskaper fungerar för närvarande bara med bilder som skapas från dokumentet knäcka steg. Därför stöds endast indata är `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Bild Analysis färdighet

Den [bildanalys färdighet](cognitive-search-skill-image-analysis.md) extraherar en omfattande uppsättning visuella funktioner baserat på innehållet i. Du kan exempelvis skapa en etikett från en avbildning, generera taggar eller identifiera kändisar och landmärken.

### <a name="ocr-skill"></a>OCR färdighet

Den [OCR färdighet](cognitive-search-skill-ocr.md) extraherar text från bildfiler som JPG, PNG-filer och bitmappar. Det kan extrahera text samt layoutinformationen. Layoutinformationen innehåller omgivande rutorna för var och en av de strängar som identifieras.

OCR-kunskaper kan du välja algoritmen som ska användas för identifiering av text i bilder. För närvarande stöder två algoritmer, en för tryckt text och en annan för handskriven text.

## <a name="embedded-image-scenario"></a>Inbäddad bild scenario

Ett vanligt scenario innebär att du skapar en sträng som innehåller alla filinnehåll, både text och text för bildens ursprungspunkt, genom att utföra följande steg:  

1. [Extrahera normalized_images](#get-normalized-images)
1. Köra OCR kunskaper med `"/document/normalized_images"` som indata
1. Sammanfoga Textrepresentationen av dessa avbildningar med rå text som extraheras från filen. Du kan använda den [Text sammanfoga](cognitive-search-skill-textmerger.md) färdighet att konsolidera både textsegment till en enda stor.

Följande exempel kompetens skapar en *merged_text* fält som innehåller det faktiska innehållet i dokumentet. Den innehåller också OCRed texten från var och en av de inbäddade bilderna. 

#### <a name="request-body-syntax"></a>Syntax för brödtexten för begäran
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

Nu när du har ett merged_text fält, kan du mappa den som ett sökbart fält i indexerarens definition. Allt innehåll på dina filer, inklusive text på bilder, blir sökbar.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualisera avgränsar rutor med extraherad text

Ett annat vanligt scenario är att visualisera layoutinformationen för search-resultaten. Du kanske vill markera där en del av texten hittades i en bild som en del av sökresultatet.

Eftersom OCR-steg utförs på normaliserad bilder, är layout koordinaterna i området normaliserade bild. När normaliserade bilden visas, förekomsten av koordinater är vanligtvis inte ett problem, men i vissa fall kanske du vill visa den ursprungliga avbildningen. Konvertera var och en av koordinaten punkter i layouten i detta fall till det ursprungliga avbildningen koordinatsystemet. 

Om du vill omvandla normaliserade koordinater till det ursprungliga koordinaten utrymmet som en hjälp kan du använda följande algoritm:

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
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Analysera bild färdighet](cognitive-search-skill-image-analysis.md)
+ [OCR färdighet](cognitive-search-skill-ocr.md)
+ [Text merge färdighet](cognitive-search-skill-textmerger.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Avancerad och mappning](cognitive-search-output-field-mapping.md)
