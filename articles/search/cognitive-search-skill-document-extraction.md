---
title: Kognitiv dokumentextrahering (förhandsgranskning)
titleSuffix: Azure Cognitive Search
description: Extraherar innehåll från en fil i anrikningspipelinen. Den här färdigheten är för närvarande i offentlig förhandsversion.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837739"
---
# <a name="document-extraction-cognitive-skill"></a>Kognitiv dokumentextrahering

> [!IMPORTANT] 
> Den här färdigheten är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Det finns för närvarande inget stöd för portal eller .NET SDK.

**Dokumentextraheringsfärdigheten** extraherar innehåll från en fil i anrikningspipelinen. På så sätt kan du dra nytta av det steg för dokumentextrahering som normalt sker innan kompetensutnyttjningen med filer som kan genereras av andra kunskaper.

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildextrahering som en del av dokumentsprickningsfasen i indexeringen. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Priser för bildutvinning beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.documentExtractionSkill

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Indata            | Tillåtna värden | Beskrivning |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Ange `default` till för dokumentextrahering från filer som inte är ren text eller json. Ställ `text` in på att förbättra prestanda på oformaterade textfiler. Ange `json` att extrahera strukturerat innehåll från json-filer. Om `parsingMode` inte definieras explicit, kommer `default`det att ställas in på . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Ange `contentAndMetadata` att extrahera alla metadata och textinnehåll från varje fil. Ange `allMetadata` att extrahera endast [innehållstyp specifika metadata](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (till exempel metadata som är unika för bara PNG-filer). Om `dataToExtract` inte definieras explicit, kommer `contentAndMetadata`det att ställas in på . |
| `configuration` | Se nedan. | En ordlista med valfria parametrar som justerar hur dokumentutextraheringen utförs. Se tabellen nedan för beskrivningar av konfigurationsegenskaper som stöds. |

| Parameter för konfiguration   | Tillåtna värden | Beskrivning |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Ställ `none` in på att ignorera inbäddade bilder eller bildfiler i datauppsättningen. Det här är standard. <br/>För [bildanalys med hjälp av kognitiva färdigheter,](cognitive-search-concept-image-scenarios.md)inställd på `generateNormalizedImages` att ha färdigheten skapa en matris med normaliserade bilder som en del av dokument sprickbildning. Den här `parsingMode` åtgärden kräver `default` `dataToExtract` att den `contentAndMetadata`är inställd på och är inställd på . En normaliserad bild refererar till ytterligare bearbetning som resulterar i enhetlig bildutmatning, storlek och roteras för att främja konsekvent rendering när du inkluderar bilder i visuella sökresultat (till exempel fotografier i samma storlek i en grafkontroll som visas i [JFK-demon).](https://github.com/Microsoft/AzureSearch_JFK_Files) Den här informationen genereras för varje bild när du använder det här alternativet.  <br/>Om du `generateNormalizedImagePerPage`ställer in på , PDF-filer kommer att behandlas annorlunda i det istället för att extrahera inbäddade bilder, kommer varje sida återges som en bild och normaliseras därefter.  Filtyper som inte är PDF-filer `generateNormalizedImages` behandlas på samma sätt som om de angavs.
| `normalizedImageMaxWidth` | Alla heltal mellan 50-10000 | Den maximala bredden (i pixlar) för normaliserade bilder som genereras. Standardvärdet är 2 000. | 
| `normalizedImageMaxHeight` | Alla heltal mellan 50-10000 | Den maximala höjden (i pixlar) för normaliserade bilder som genereras. Standardvärdet är 2 000. |

> [!NOTE]
> Standardvärdet på 2 000 pixlar för de normaliserade bildernas maximala bredd och höjd baseras på de maximala storlekar som stöds av [OCR-färdigheten](cognitive-search-skill-ocr.md) och [bildanalysfärdigheten](cognitive-search-skill-image-analysis.md). [OCR-färdigheten](cognitive-search-skill-ocr.md) stöder en maximal bredd och höjd på 4200 för icke-engelska språk och 10000 för engelska.  Om du ökar maxgränserna kan bearbetningen misslyckas med större bilder beroende på din kompetensdefinition och dokumentens språk. 
## <a name="skill-inputs"></a>Indata för färdighet

| Indatanamn     | Beskrivning |
|--------------------|-------------|
| file_data | Filen som innehållet ska extraheras från. |

Indata för "file_data" måste vara ett objekt som definieras på följande sätt:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Det här filreferensobjektet kan genereras på ett av tre sätt:

 - Ställa `allowSkillsetToReadFileData` in parametern på indexeringsdefinitionen på "true".  Då skapas `/document/file_data` en sökväg som är ett objekt som representerar de ursprungliga fildata som hämtats från blob-datakällan. Den här parametern gäller endast för data i Blob-lagring.

 - Ange `imageAction` parametern på indexeringsdefinitionen `none`på ett annat värde än .  Detta skapar en matris med bilder som följer den nödvändiga konventionen för inmatning `/document/normalized_images/*`till den här färdigheten om den skickas individuellt (dvs. ).

 - Om du har en anpassad färdighet returnerar ett json-objekt som definieras exakt som ovan.  Parametern `$type` måste anges `file` till `data` exakt och parametern måste vara bas 64-kodade bytematrisdata för filinnehållet.

## <a name="skill-outputs"></a>Utdata för färdighet

| Utdatanamn    | Beskrivning |
|--------------|-------------|
| innehåll | Dokumentets textinnehåll. |
| normalized_images | När `imageAction` värdet är inställt på `none`ett annat värde innehåller det nya *normalized_images* fältet en matris med bilder. Mer information om utdataformatet för varje bild finns [i dokumentationen för bildextrahering.](cognitive-search-concept-image-scenarios.md) |

##  <a name="sample-definition"></a>Exempeldefinition

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

##  <a name="sample-input"></a>Exempelinmatning

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

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Så här bearbetar och extraherar du information från bilder i kognitiva sökscenarier](cognitive-search-concept-image-scenarios.md)