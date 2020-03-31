---
title: Extrahera text från bilder
titleSuffix: Azure Cognitive Search
description: Bearbeta och extrahera text och annan information från avbildningar i Azure Cognitive Search-pipelines.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98054060210f55803d6e2811e1f494fd3ff00e48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76838266"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Så här bearbetar och extraherar du information från bilder i AI-anrikningsscenarier

Azure Cognitive Search har flera funktioner för att arbeta med avbildningar och bildfiler. Under dokumentsprickning kan du använda parametern *imageAction* för att extrahera text från foton eller bilder som innehåller alfanumerisk text, till exempel ordet "STOP" i ett stopptecken. Andra scenarier är att generera en textrepresentation av en bild, till exempel "maskros" för ett foto av en maskros, eller färgen "gul". Du kan också extrahera metadata om bilden, till exempel dess storlek.

Den här artikeln beskriver bildbehandling mer i detalj och ger vägledning för att arbeta med bilder i en AI-anrikningspipeline.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Få normaliserade bilder

Som en del av dokumentsprickor finns det en ny uppsättning indexeringskonfigurationsparametrar för hantering av bildfiler eller bilder inbäddade i filer. Dessa parametrar används för att normalisera bilder för ytterligare nedströmsbearbetning. Normalisering av bilder gör dem mer enhetliga. Stora bilder ändras till en maximal höjd och bredd för att göra dem förbrukningsbara. För bilder som tillhandahåller metadata om orientering justeras bildrotationen för vertikal inläsning. Metadatajusteringar fångas in i en komplex typ som skapas för varje bild. 

Du kan inte stänga av bildens normalisering. Färdigheter som itererar över bilder förväntar sig normaliserade bilder. För att aktivera bildnormalisering på en indexerare krävs att en kompetens kopplas till den indexeraren.

| Parameter för konfiguration | Beskrivning |
|--------------------|-------------|
| bildAction   | Ange "ingen" om ingen åtgärd ska vidtas när inbäddade bilder eller bildfiler påträffas. <br/>Ställ in på "generateNormalizedImages" för att generera en rad normaliserade bilder som en del av dokumentsprickor.<br/>Ställ in på "generateNormalizedImagePerPage" för att generera en matris med normaliserade bilder där varje sida återges till en utdatabild för PDF-filer i datakällan.  Funktionaliteten är densamma som "generateNormalizedImages" för icke-PDF-filtyper.<br/>För alla alternativ som inte är "ingen" kommer bilderna att exponeras i *fältet normalized_images.* <br/>Standard är "ingen". Den här konfigurationen är endast relevant för blob-datakällor, när "dataToExtract" är inställt på "contentAndMetadata". <br/>Högst 1000 bilder kommer att extraheras från ett visst dokument. Om det finns fler än 1 000 bilder i ett dokument extraheras de första 1000 och en varning genereras. |
|  normaliseradBildbildMaxWidth | Den maximala bredden (i pixlar) för normaliserade bilder som genereras. Standardvärdet är 2 000. Det högsta tillåtna värdet är 10000. | 
|  normaliseradBildbildaMaxHeight | Den maximala höjden (i pixlar) för normaliserade bilder som genereras. Standardvärdet är 2 000. Det högsta tillåtna värdet är 10000.|

> [!NOTE]
> Om du ställer in egenskapen *imageAction* till något annat än "ingen", kan du inte ställa in *egenskapen parsingMode* till något annat än "standard".  Du kan bara ange en av dessa två egenskaper till ett icke-standardvärde i indexerkonfigurationen.

Ange **parametern parsingMode** till (för att `json` indexera `jsonArray` varje blob som ett enda dokument) eller (om blobbar innehåller JSON-matriser och du behöver varje element i en matris som ska behandlas som ett separat dokument).

Standardvärdet på 2 000 pixlar för de normaliserade bildernas maximala bredd och höjd baseras på de maximala storlekar som stöds av [OCR-färdigheten](cognitive-search-skill-ocr.md) och [bildanalysfärdigheten](cognitive-search-skill-image-analysis.md). [OCR-färdigheten](cognitive-search-skill-ocr.md) stöder en maximal bredd och höjd på 4200 för icke-engelska språk och 10000 för engelska.  Om du ökar maxgränserna kan bearbetningen misslyckas med större bilder beroende på din kompetensdefinition och dokumentens språk. 

Du anger bildenÅtgärder i [indexeringsdefinitionen](https://docs.microsoft.com/rest/api/searchservice/create-indexer) enligt följande:

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

När *bildenAction* är inställd på ett annat värde än "ingen", kommer det nya *normalized_images* fältet innehålla en matris med bilder. Varje bild är en komplex typ som har följande medlemmar:

| Bildmedlem       | Beskrivning                             |
|--------------------|-----------------------------------------|
| data               | BASE64 kodade strängen för den normaliserade bilden i JPEG-format.   |
| bredd              | Bredden på den normaliserade bilden i pixlar. |
| höjd             | Höjden på den normaliserade bilden i pixlar. |
| originalBredd      | Bildens ursprungliga bredd före normalisering. |
| originalHeight      | Den ursprungliga höjden på bilden före normalisering. |
| rotationFrånOriginal |  Moturs rotation i grader som inträffade för att skapa den normaliserade bilden. Ett värde mellan 0 grader och 360 grader. Det här steget läser metadata från bilden som genereras av en kamera eller skanner. Vanligtvis en multipel av 90 grader. |
| innehållOffset | Teckenförskjutningen i innehållsfältet där bilden extraherades från. Det här fältet är endast tillämpligt för filer med inbäddade bilder. |
| Pagenumber | Om bilden extraherades eller återges från en PDF-fil innehåller det här fältet sidnumret i pdf-filen som den extraherades eller återges från från och med 1.  Om bilden inte kom från en PDF-fil blir det här fältet 0.  |

 Provvärde *för normalized_images:*
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Bildrelaterade färdigheter

Det finns två inbyggda kognitiva färdigheter som tar bilder som en ingång: [OCR](cognitive-search-skill-ocr.md) och [bildanalys](cognitive-search-skill-image-analysis.md). 

För närvarande fungerar dessa kunskaper endast med bilder som genereras från dokumentsprickningssteget. Den enda indata som `"/document/normalized_images"`stöds är därför .

### <a name="image-analysis-skill"></a>Bildanalys skicklighet

Med [bildanalysen](cognitive-search-skill-image-analysis.md) extraheras en omfattande uppsättning visuella funktioner baserat på bildinnehållet. Du kan till exempel generera en bildtext från en bild, generera taggar eller identifiera kändisar och landmärken.

### <a name="ocr-skill"></a>OCR-skicklighet

[OCR-färdigheten](cognitive-search-skill-ocr.md) extraherar text från bildfiler som JPGs, PNGs och bitmappar. Det kan extrahera text samt layoutinformation. Layoutinformationen innehåller begränsningsrutor för var och en av de identifierade strängarna.

## <a name="embedded-image-scenario"></a>Inbäddat bildscenario

Ett vanligt scenario innebär att skapa en enda sträng som innehåller allt filinnehåll, både text och text om bildursprung, genom att utföra följande steg:  

1. [Utdrag normalized_images](#get-normalized-images)
1. Kör OCR-färdigheten med som `"/document/normalized_images"` indata
1. Sammanfoga textrepresentationen av dessa bilder med den råa text som extraherats från filen. Du kan använda [färdigheten Koppla text](cognitive-search-skill-textmerger.md) för att konsolidera båda textsegmenten till en enda stor sträng.

I följande exempel skapar kunskaper ett *merged_text* fält som innehåller dokumentets textinnehåll. Den innehåller också OCRed text från var och en av de inbäddade bilderna. 

#### <a name="request-body-syntax"></a>Syntax för begäran om brödtext
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

Nu när du har ett merged_text fält kan du mappa det som ett sökbart fält i indexeringsdefinitionen. Allt innehåll i dina filer, inklusive texten i bilderna, kommer att vara sökbart.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualisera markeringsrutor med extraherad text

Ett annat vanligt scenario är att visualisera layoutinformation för sökresultat. Du kanske till exempel vill markera var en textbit hittades i en bild som en del av sökresultaten.

Eftersom OCR-steget utförs på de normaliserade bilderna finns layoutkoordinaterna i det normaliserade bildutrymmet. När du visar den normaliserade bilden är förekomsten av koordinater i allmänhet inte ett problem, men i vissa situationer kanske du vill visa den ursprungliga bilden. I det här fallet konverterar var och en av koordinatpunkterna i layouten till det ursprungliga bildkoordinatsystemet. 

Som hjälprepare kan du använda följande algoritm om du behöver omvandla normaliserade koordinater till det ursprungliga koordinatutrymmet:

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

## <a name="see-also"></a>Se även
+ [Skapa indexerare (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Bildanalys skicklighet](cognitive-search-skill-image-analysis.md)
+ [OCR-skicklighet](cognitive-search-skill-ocr.md)
+ [Färdighet för kopplad text](cognitive-search-skill-textmerger.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Så här mappar du berikade fält](cognitive-search-output-field-mapping.md)
