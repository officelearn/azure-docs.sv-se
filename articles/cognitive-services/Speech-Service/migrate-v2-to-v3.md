---
title: Migrera från v2 till v3 REST API-Speech service
titleSuffix: Azure Cognitive Services
description: Jämfört med v2 innehåller den nya API-versionen v3 en uppsättning mindre avbrytande ändringar. I det här dokumentet får du hjälp med att migrera till den nya huvud versionen på en gång.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96738028"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Migrering från v 2.0 till v 3.0 med tal till text REST API

V3-versionen av tal REST API förbättras över den tidigare API-versionen avseende tillförlitlighet och enkel användning. API-layouten anpassas närmare med andra Azure-eller API:er för Cognitive Services. Detta hjälper dig att använda dina befintliga kunskaper när du använder vårt tal-API.

API-översikten är tillgänglig som ett [Swagger-dokument](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Detta är idealiskt för att ge dig en API-översikt och testa det nya API: et.

Vi tillhandahåller exempel för C# och python. För batch-avskrifter hittar du exemplen i [GitHub-exempel lagret](https://aka.ms/csspeech/samples) i under `samples/batch` katalogen.

## <a name="forward-compatibility"></a>Vidarebefordra kompatibilitet

För att säkerställa en smidig migrering till v3 kan alla entiteter från v2 också finnas i v3-API: et under samma identitet. Om det finns en resultat schema ändring (till exempel avskrifter), kommer Svaren för en GET i v3-version av API: et att finnas i v3-schemat. Om du kör GET i v2-versionen av API: et, är resultat schemat i v2-format. Nyligen skapade entiteter på v3 **är inte** tillgängliga på v2.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Listan över avbrytande ändringar har sorterats efter storleken på de ändringar som krävs för att anpassa. Det finns bara några ändringar som kräver en icke-trivial ändring i anrops koden. De flesta ändringar kräver enkla omnamn. Hur lång tid det tog för team att migrera från v2 till v3 mellan några timmar till några dagar. Men fördelarna med ökad stabilitet, enklare kod, snabbare svar kan snabbt kompensera investeringen. 

### <a name="host-name-changes"></a>Värd namns ändringar

Värd namnen har ändrats från {region}. CRI: er. AI till {region}. API. kognitiv. Microsoft. com. I den här ändringen innehåller Sök vägarna inte längre "API/" eftersom den ingår i värd namnet. Se [Swagger-dokumentet](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) för en fullständig beskrivning av regioner och sökvägar.

### <a name="identity-of-an-entity"></a>Identitet för en entitet

Egenskapen `id` har ersatts med `self` . I v2 var en API-användare medveten om hur våra sökvägar i API: et skapades. Detta var inte utöknings Bart och krävde onödigt arbete från användaren. Egenskapen `id` (UUID) ersätts av `self` (sträng), vilket är platsen för entiteten (URL). Värdet är fortfarande unikt mellan alla dina entiteter. Om `id` lagras som en sträng i koden räcker det med en enkel namnbyte för att stödja det nya schemat. Du kan nu använda `self` innehållet som URL för alla rest-anrop för entiteten (get, patch, Delete).

Om entiteten har ytterligare funktioner som är tillgängliga under andra sökvägar visas de under `links` . Ett exempel är en avskrift, som har en separat metod för `GET` innehållet i avskriften.

v2-avskrift:

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

v3-avskriftering:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Beroende på klient implementeringen är det inte tillräckligt för att byta namn på egenskapen. Vi rekommenderar att du drar nytta av de returnerade värdena för `self` och `links` som mål-URL: er för dina rest-anrop och inte genererar Sök vägarna i klienten. Genom att använda de returnerade URL: erna kan du vara säker på att framtida ändringar i sökvägar inte kommer att bryta din klient kod.

### <a name="working-with-collections-of-entities"></a>Arbeta med samling av entiteter

Tidigare returnerade v2-API: n alla tillgängliga entiteter i ett svar. För att ge en mer detaljerad kontroll över förväntad svars storlek visas sid brytningar i v3. Du har kontroll över antalet returnerade entiteter och sidans förskjutning. Det här beteendet gör det enkelt att förutsäga körningen av svars processorn och att den är konsekvent med andra Azure-API: er.

Den grundläggande formen för svaret är samma för alla samlingar:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

Egenskapen `values` innehåller en delmängd av de tillgängliga samlings enheterna. Antalet och förskjutningen kan kontrol leras med hjälp av frågeparametrar `skip` och `top` . Om `@nextLink` inte är null finns det fler data och nästa batch med data kan hämtas genom att göra en hämtning `$.@nextLink` .

Den här ändringen kräver att du anropar `GET` for-samlingen i en loop tills alla element har returnerats.

### <a name="creating-transcriptions"></a>Skapa avskrifter

En detaljerad beskrivning av hur du skapar avskrifter finns i [batch-avskriftering](./batch-transcription.md).

Skapandet av avskrifter har ändrats i v3 för att aktivera inställning av alternativ för specifik avskrift. Alla (valfria) konfigurations egenskaper kan nu anges i `properties` egenskapen.
Nu stöder version v3 flera indatafiler och därför krävs en lista över URL: er och inte en enskild URL-adress som krävs av v2. Egenskaps namnet har bytt namn från `recordingsUrl` till `contentUrls` . Funktionerna för att analysera sentiment i avskrifter har tagits bort från v3. Vi rekommenderar att du använder [text analysen](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) i Microsoft kognitiva tjänster i stället.

Den nya egenskapen `timeToLive` under `properties` kan hjälpa till att rensa befintliga slutförda entiteter. `timeToLive`Anger en varaktighet efter vilken en slutförd entitet tas bort automatiskt. Ange det till ett högt värde (till exempel `PT12H` ) när entiteterna kontinuerligt spåras, förbrukas och tas bort och därför vanligt vis bearbetas långt innan 12 timmar skickas.

v2-avskrifts INLÄGGets brödtext:

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

v3-avskrifts INLÄGGets brödtext:

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>Format på v3-avskrifts resultat

Schemat för avskrifts resultat har ändrats något för att anpassas efter avskrifter som skapats av slut punkter i real tid. En djupgående Beskrivning av det nya formatet finns i [batch-avskrifts instruktionen](./batch-transcription.md). Schemat för resultatet publiceras i vår [GitHub-exempel databas](https://aka.ms/csspeech/samples) under `samples/batch/transcriptionresult_v3.schema.json` .

Egenskaps namnen är nu kamel notation-bokstäver och värdena för Channel och talare använder heltals typer. För att justera formatet på varaktigheter med andra Azure API: er, är det nu i formaterat enligt beskrivningen i ISO 8601.

Exempel på ett v3-avskrifts resultat. Skillnaderna beskrivs i kommentarerna.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="getting-the-content-of-entities-and-the-results"></a>Hämtar innehållet i entiteter och resultaten

I v2 har länkar till indata-eller resultat filerna infogats i resten av entitetens metadata. Som en förbättring av v3 finns det en tydlig åtskillnad mellan entitetens metadata, som returneras av en hämtning `$.self` och information och autentiseringsuppgifter för att få åtkomst till resultat filen. Den här separationen hjälper till att skydda kund information och ger en noggrann kontroll över giltighets tiden för autentiseringsuppgifterna.

I v3 finns en egenskap som kallas `files` för länkar, om enheten visar data (data uppsättningar, avskrifter, slut punkter, utvärderingar). Ett kom igång `$.links.files` returnerar en lista över filer och SAS-URL för att få åtkomst till innehållet i varje fil. Om du vill kontrol lera giltighets tiden för SAS-URL: erna `sasValidityInSeconds` kan du använda Frågeparametern för att ange livs längd.

v2-avskrift:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

v3-avskriftering:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Sedan leder ett GET till `$.links.files` :

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`Anger formatet på filens innehåll. För avskrifter är fil typerna `TranscriptionReport` sammanfattningen av jobbet och filer av typen `Transcription` är resultatet av själva jobbet.

### <a name="customizing-models"></a>Anpassa modeller

Före v3 var det en skillnad mellan en "akustisk modell" och en "språk modell" när en modell tränades. Den här skillnaden ledde till att du måste ange flera modeller när du skapar slut punkter eller avskrifter. För att förenkla den här processen för en anropare har vi tagit bort skillnaderna och gjort allt beroende av innehållet i de data uppsättningar som används för modell träning. Med den här ändringen stöder modell skapande nu blandade data uppsättningar (språk data och akustiska data). Slut punkter och avskrifter kräver nu bara en modell.

Med den här ändringen har behovet för a `kind` i inlägget tagits bort och `datasets[]` kan nu innehålla flera data uppsättningar av samma eller blandade typer.

För att förbättra resultatet av en utbildad modell används akustiska data automatiskt internt för språkutbildningen. I allmänhet ger modeller som skapats via v3-API: n mer exakta resultat än modeller som skapats med v2-API: et.

### <a name="retrieving-base-and-custom-models"></a>Hämtar bas-och anpassade modeller

För att förenkla hämtningen av tillgängliga modeller, har v3 separerat samlingarna av "bas modeller" från kundägda "anpassade modeller". De två vägarna är nu `GET /speechtotext/v3.0/models/base` och `GET /speechtotext/v3.0/models/` .

Tidigare har alla modeller returnerats tillsammans i ett enda svar.

### <a name="name-of-an-entity"></a>Namn på en entitet

Namnet på egenskapen `name` har bytt namn till `displayName` . Detta är justerat till andra Azure-API: er för att inte indikera identitets egenskaper. Värdet för den här egenskapen får inte vara unikt och kan ändras efter att entiteten har skapats med en `PATCH` .

v2-avskrift:

```json
{
    "name": "Transcription using locale en-US"
}
```

v3-avskriftering:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Åtkomst till refererade entiteter

I v2 refererade entiteter har alltid infogats, till exempel de använda modellerna för en slut punkt. Kapslade entiteter resulterade i stora svar och konsumenter förbrukade sällan det kapslade innehållet. Om du vill minska svars storleken och förbättra prestandan för alla API-användare är de refererade entiteterna inte längre i svaret. I stället används en referens till den andra entiteten som kan användas direkt. Den här referensen kan användas för efterföljande `GET` (det är en webb adress också), och följer samma mönster som `self` länken.

v2-avskrift:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
          
        }
      ]
    },
  ]
}
```

v3-avskriftering:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Om du behöver använda informationen om en refererad modell, som du ser i exemplet ovan, kan du förenkla utfärda en hämtning `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Hämtar slut punkts loggar

Version v2 av tjänsten som stöds loggar svaren på slut punkter. För att hämta resultatet av en slut punkt med v2 var det en tvungen att skapa en "data export", som representerade en ögonblicks bild av resultaten som definierats i ett tidsintervall. Processen för att exportera batchar av data har blivit inflexibla. V3-API ger åtkomst till varje enskild fil och möjliggör upprepning genom dem.

En v3-slutpunkt som körs:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Svar på GET `$.links.logs` :

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

Sid brytning för slut punkts loggar fungerar ungefär som alla andra samlingar, förutom att ingen förskjutning kan anges. På grund av den stora mängden tillgängliga data måste Server driven sid brytning implementeras.

I v3 kan varje slut punkts logg tas bort individuellt genom att utfärda en borttagning på `self` en fil eller genom att använda ta bort på `$.links.logs` . Om du vill ange en slut data, kan du lägga till frågeparametern i `endDate` begäran.

### <a name="using-custom-properties"></a>Använda anpassade egenskaper

Om du vill avgränsa anpassade egenskaper från valfria konfigurations egenskaper finns alla explicit namngivna egenskaper i `properties` egenskapen och alla egenskaper som definierats för anroparna finns nu i `customProperties` egenskapen.

v2-avskrifts enhet

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

v3-avskrifts enhet

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Den här ändringen aktiverade också användningen av korrekta typer på alla explicit namngivna egenskaper under `properties` (till exempel bool i stället för sträng).

### <a name="response-headers"></a>Svarshuvuden

v3 returnerar inte längre rubriken `Operation-Location` förutom sidhuvudet `Location` för post-begäranden. Värdet för båda rubrikerna som används som exakt samma. Nu `Location` returneras bara.

Eftersom den nya API-versionen nu hanteras av Azure API Management (APIM), är begränsningen av relaterade huvuden `X-RateLimit-Limit` , `X-RateLimit-Remaining` och `X-RateLimit-Reset` inte i svars huvudena.

### <a name="accuracy-tests"></a>Test av precision

Noggrannhets test har bytt namn till utvärderingar eftersom det nya namnet beskriver bättre vad de representerar. Nyhets Sök vägarna är till exempel "https://{region}. API. kognitiva. Microsoft. com/speechtotext/v 3.0/utvärderingar".
