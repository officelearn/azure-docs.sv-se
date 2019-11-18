---
title: Dokument extrahering kognitiv kunskap (för hands version)
titleSuffix: Azure Cognitive Search
description: Extraherar innehåll från en fil inom en pipeline för anrikning. Den här kunskapen är för närvarande en offentlig för hands version.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: a79b0476fccbd2e2b9d3cf47ecfdc99c17c2862d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113332"
---
# <a name="document-extraction-cognitive-skill"></a>Inlärnings kunskap för dokument extrahering

> [!IMPORTANT] 
> Den här kunskapen är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API version 2019-05-06-Preview](search-api-preview.md) innehåller för hands versions funktioner. Det finns för närvarande inget stöd för Portal eller .NET SDK.

**Dokument extraherings** kunskapen extraherar innehåll från en fil i pipelinen för anrikning. På så sätt kan du utnyttja det steg för dokument extrahering som normalt inträffar innan färdigheter-körningen med filer som kan genereras av andra färdigheter.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av stadiet dokument sprickor i indexering. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft. färdigheter. util. DocumentExtractionSkill

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga.

| Indata            | Tillåtna värden | Beskrivning |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Ange till `default` för dokument extrahering från filer som inte är rena text eller JSON. Ange till `text` för att förbättra prestandan för filer med oformaterad text. Ange till `json` om du vill extrahera strukturerat innehåll från JSON-filer. Om `parsingMode` inte uttryckligen definieras anges det som `default`. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Ange till `contentAndMetadata` för att extrahera alla metadata och text innehåll från varje fil. Ange till `allMetadata` för att endast extrahera de metadata som är [specifika för innehålls typen](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (till exempel metadata som är unika för just. png-filer). Om `dataToExtract` inte uttryckligen definieras anges det som `contentAndMetadata`. |
| `configuration` | Se nedan. | En ord lista med valfria parametrar som justerar hur dokument extraheringen utförs. I tabellen nedan finns beskrivningar av de konfigurations egenskaper som stöds. |

| Konfigurations parameter   | Tillåtna värden | Beskrivning |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Ange till `none` om du vill ignorera inbäddade bilder eller bildfiler i data uppsättningen. Detta är standardinställningen. <br/>För [bild analys med kognitiva kunskaper](cognitive-search-concept-image-scenarios.md)kan du ställa in på `generateNormalizedImages` så att kunskapen skapar en matris med normaliserade avbildningar som en del av dokument sprickor. Den här åtgärden kräver att `parsingMode` har angetts till `default` och `dataToExtract` har angetts till `contentAndMetadata`. En normaliserad bild syftar på ytterligare bearbetning som resulterar i enhetlig bild utmatning, storleks ändring och rotation för att främja konsekvent åter givning när du inkluderar bilder i visuella Sök resultat (till exempel samma storlek i fotografier i en diagram kontroll som visas i [JFK demo](https://github.com/Microsoft/AzureSearch_JFK_Files)). Den här informationen genereras för varje avbildning när du använder det här alternativet.  <br/>Om du anger till `generateNormalizedImagePerPage`kommer PDF-filer att behandlas på ett annat sätt i stället för att extrahera inbäddade bilder. varje sida återges som en bild och normaliseras därefter.  Filtyper som inte är PDF-filer kommer att behandlas på samma sätt som om `generateNormalizedImages` har angetts.
| `normalizedImageMaxWidth` | Ett heltal mellan 50-10000 | Den maximala bredden (i bild punkter) för normaliserade bilder som genereras. Standardvärdet är 2000. | 
| `normalizedImageMaxHeight` | Ett heltal mellan 50-10000 | Den maximala höjden (i bild punkter) för normaliserade bilder som genereras. Standardvärdet är 2000. |

> [!NOTE]
> Standardvärdet på 2000 bild punkter för de normaliserade bildernas maximala bredd och höjd baseras på de maximala storlekar som stöds av [OCR-kompetensen](cognitive-search-skill-ocr.md) och [bild analysens färdighet](cognitive-search-skill-image-analysis.md). [OCR-kunskaper](cognitive-search-skill-ocr.md) stöder maximal bredd och höjd på 4200 för andra språk än engelska och 10000 för engelska.  Om du ökar Max gränsen kan bearbetningen av större avbildningar gå sönder beroende på din färdigheter-definition och dokumentets språk. 
## <a name="skill-inputs"></a>Kompetens inmatningar

| Inmatat namn     | Beskrivning |
|--------------------|-------------|
| file_data | Filen som innehållet ska extraheras från. |

Inmatade file_data måste vara ett objekt som definieras enligt följande:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Detta fil referens objekt kan genereras av tre sätt:

 - Ställer in parametern `allowSkillsetToReadFileData` i din index-definition till "true".  Detta skapar en sökväg `/document/file_data` som är ett objekt som representerar de ursprungliga fil data som hämtats från BLOB-datakällan. Den här parametern gäller endast för data i Blob Storage.

 - Ange ett annat värde än `none`för parametern `imageAction` på indexeraren.  Detta skapar en matris med bilder som följer den konvention som krävs för inmatade kunskaper om den har skickats separat (d.v.s. `/document/normalized_images/*`).

 - En anpassad kunskap returnerar ett JSON-objekt som definieras exakt som ovan.  Parametern `$type` måste anges till exakt `file` och `data`s parametern måste vara bas 64-kodade byte mat ris data för fil innehållet.

## <a name="skill-outputs"></a>Kunskaps utmatningar

| Namn på utdata    | Beskrivning |
|--------------|-------------|
| innehåll | Dokumentets text innehåll. |
| normalized_images | Om `imageAction` har angetts till ett annat värde än `none`, innehåller fältet ny *normalized_images* en matris med bilder. I [dokumentationen för avbildnings extrahering](cognitive-search-concept-image-scenarios.md) finns mer information om utdataformatet för varje bild. |

##  <a name="sample-definition"></a>Exempel definition

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Exempel på inmatade

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Exempel på utdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Se även

+ [Inbyggda kunskaper](cognitive-search-predefined-skills.md)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Så här bearbetar och extraherar du information från bilder i kognitiva Sök scenarier](cognitive-search-concept-image-scenarios.md)