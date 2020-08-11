---
title: Vad är batch-avskrifter – tal service
titleSuffix: Azure Cognitive Services
description: Batch-avskrift är idealiskt om du vill skriva över en stor mängd ljud i lagring, till exempel Azure-blobbar. Genom att använda den dedikerade REST API kan du peka på ljudfiler med en SAS-URI (signatur för delad åtkomst) och få asynkront erhålla avskrifter.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 3e7f310f37bd016a73c589db3c9a23e197465427
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053924"
---
# <a name="what-is-batch-transcription"></a>Vad är batch-avskriftering?

Batch-avskrift är en uppsättning REST API åtgärder som gör det möjligt att skriva av en stor mängd ljud i lagring. Du kan peka på ljudfiler med en SAS-URI (signatur för delad åtkomst) och få svars resultat asynkront. Med New v 3.0-API: et kan du välja att skriva en eller flera ljudfiler eller bearbeta en hel lagrings behållare.

Asynkront tal-till-text-avskrift är bara en av funktionerna. Du kan använda REST-API: er för batch-avskrift för att anropa följande metoder:



|    Batch-avskrifts åtgärd                                             |    Metod    |    REST API-anrop                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Skapar en ny avskrift.                                              |    POST      |    speechtotext/v 3.0/avskrifter            |
|    Hämtar en lista över avskrifter för den autentiserade prenumerationen.    |    GET       |    speechtotext/v 3.0/avskrifter            |
|    Hämtar en lista över språk som stöds för offline-avskrifter.              |    GET       |    speechtotext/v 3.0/avskrifter/språk    |
|    Uppdaterar föränderligt-informationen för avskriften som identifieras av dess ID.    |    9.0a     |    speechtotext/v 3.0/avskrifter/{ID}       |
|    Tar bort den angivna avskrifts uppgiften.                                 |    DELETE    |    speechtotext/v 3.0/avskrifter/{ID}       |
|    Hämtar avskriften som identifieras av det angivna ID: t.                        |    GET       |    speechtotext/v 3.0/avskrifter/{ID}       |
|    Hämtar resultat filen för avskriften som identifieras av det angivna ID: t.    |    GET       |    speechtotext/v 3.0/avskrifter/{ID}/filer |




Du kan granska och testa det detaljerade API: et, som är tillgängligt som ett [Swagger-dokument](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Jobb för batch-avskrifter schemaläggs enligt bästa prestanda. Det finns för närvarande ingen uppskattning för när ett jobb ändras till körnings tillstånd. Under normal system belastning bör det ske inom några minuter. När den faktiska avskriften bearbetas fort i körnings tillstånd bearbetas den faktiska avskriften än ljudet i real tid.

Bredvid det lättanvända API: t behöver du inte distribuera anpassade slut punkter och du har inte några samtidiga krav att följa.

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscription-key"></a>Prenumerationsnyckel

Precis som med alla funktioner i tal tjänsten skapar du en prenumerations nyckel från [Azure Portal](https://portal.azure.com) genom att följa vår [Guide för att komma igång](get-started.md).

>[!NOTE]
> En standard prenumeration (S0) för tal tjänst krävs för att använda batch-avskriftering. Kostnads fria prenumerations nycklar (F0) fungerar inte. Mer information finns i [priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Anpassade modeller

Om du planerar att anpassa modeller följer du stegen i [akustisk anpassning](how-to-customize-acoustic-models.md) och [språk anpassning](how-to-customize-language-model.md). Om du vill använda de skapade modellerna i batch-avskriftering behöver du deras modell plats. Du kan hämta modell platsen när du har granskat informationen om modellen ( `self` egenskap). Det *behövs ingen* distribuerad anpassad slut punkt för batch-avskrifts tjänsten.

## <a name="the-batch-transcription-api"></a>API för batch-avskriftering

### <a name="supported-formats"></a>Format som stöds

API: et för batch-avskrift stöder följande format:

| Format | ADPCM | Hastigheten | Samplings frekvens                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bitars  | 8 kHz eller 16 kHz, mono eller stereo |
| MP3    | PCM   | 16-bitars  | 8 kHz eller 16 kHz, mono eller stereo |
| OGG    | OPUS  | 16-bitars  | 8 kHz eller 16 kHz, mono eller stereo |

För stereo ljud strömmar delas vänster och höger kanaler upp under avskriften. För varje kanal skapas en JSON-resultat fil. De tidsstämplar som genereras per uttryck gör att utvecklaren kan skapa en sorterad slutlig avskrift.

### <a name="configuration"></a>Konfiguration

Konfigurations parametrar tillhandahålls som JSON (en eller flera enskilda filer):

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Konfigurations parametrar tillhandahålls som JSON (bearbetning av en hel lagrings behållare):

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

Om du vill använda anpassade, utbildade modeller i batch-avskrifter kan de refereras till som visas nedan:

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Konfigurations egenskaper

Använd dessa valfria egenskaper för att konfigurera avskrifter:

:::row:::
   :::column span="1":::
      **Parameter**
   :::column-end:::
   :::column span="2":::
      **Beskrivning**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Anger hur du hanterar svordomar i igenkännings resultat. Godkända värden är `None` att inaktivera filtrering av svordomar, `Masked` för att ersätta svordomar med asterisker, `Removed` för att ta bort alla svordomar från resultatet eller `Tags` för att lägga till taggar av "svordomar". Standardinställningen är `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Anger hur interpunktion ska hanteras i igenkännings resultat. Godkända värden är `None` att inaktivera interpunktion, `Dictated` för att göra det explicit (talade) skiljetecken `Automatic` att tillåta avkodaren att hantera skiljetecken eller `DictatedAndAutomatic` att använda dikterad och automatisk interpunktion. Standardinställningen är `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Anger om Word-nivåns tidsstämplar ska läggas till i utdata. Godkända värden är `true` att aktivera Word-nivåns tidsstämplar och `false` (standardvärdet) för att inaktivera det.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Anger att diarization-analys ska utföras på indatamängden, vilket förväntas vara en svartvit kanal som innehåller två röster. Godkända värden `true` aktiverar diarization och `false` (standardvärdet) för att inaktivera det. Det måste också `wordLevelTimestampsEnabled` anges till sant.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      En valfri matris med kanal nummer att bearbeta. Här kan du ange en delmängd av tillgängliga kanaler i ljud filen som ska bearbetas (t. ex. `0` endast). Om inget `0` värde anges används kanaler och `1` som standard.
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      En valfri varaktighet för att automatiskt ta bort avskrifter när avskriften har slutförts. `timeToLive`Är användbart vid Mass bearbetnings avskrifter för att se till att de kommer att tas bort (t. ex. `PT12H` ). Om detta inte anges eller anges till `PT0H` , tas inte avskriften bort automatiskt.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Valfri URL med [tjänstens SAS](../../storage/common/storage-sas-overview.md) till en skrivbar behållare i Azure. Resultatet lagras i den här behållaren. Om detta inte anges lagrar Microsoft resultaten i en lagrings behållare som hanteras av Microsoft. När avskriften tas bort genom att anropar [ta bort avskrift](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)tas även resultat data bort.
:::row-end:::

### <a name="storage"></a>Storage

Batch-avskrift stöder [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) för att läsa ljud och skriva avskrifter till lagring.

## <a name="the-batch-transcription-result"></a>Resultatet av batch-avskriften

För varje ljud inspelning skapas en resultat fil för avskriften. Du kan hämta listan över resultat filer genom att anropa [Get deskrifts-filer](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles). Den här metoden returnerar en lista med resultat filer för den här avskriften. Om du vill hitta avskrifts filen för en speciell indatafil filtrerar du alla returnerade filer med `kind`  ==  `Transcription` och `name`  ==  `{originalInputName.suffix}.json` .

Varje avskrifts resultat fil i detta format:

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
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

Resultatet innehåller följande formulär:

:::row:::
   :::column span="1":::
      **Formulär**
   :::column-end:::
   :::column span="2":::
      **Innehåll**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      De faktiska orden som identifieras.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Inverterad text – normaliserad text för den tolkade texten. Förkortningar ("läkare Smith" till "Dr Smith"), telefonnummer och andra transformeringar tillämpas.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      REDUNDANSVÄXLINGAR-formuläret med en svordom som används.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      Den tolkade textens visnings form. Tillagd interpunktion och versaler ingår.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Högtalar separation (Diarization)

Diarization är en process för att åtskilja högtalare i ett ljud. Vår batch-pipeline stöder diarization och kan identifiera två högtalare på svartvita kanal inspelningar. Funktionen är inte tillgänglig i stereo inspelningar.

Resultatet av avskrift med diarization aktiverat innehåller en `Speaker` post för varje uppskriven fras. Om diarization inte används finns `Speaker` inte egenskapen i JSON-utdata. För diarization har vi stöd för två röster, så att högtalarna identifieras som `1` eller `2` .

Om du vill begära diarization behöver du bara lägga till relevant parameter i HTTP-begäran som visas nedan.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Tidsstämplar på Word-nivå måste vara aktiverade eftersom parametrarna i ovanstående begäran visar.

## <a name="best-practices"></a>Bästa praxis

Avskrifts tjänsten kan hantera ett stort antal skickade avskrifter. Du kan ställa frågor om status för dina avskrifter via en `GET` på [Get-avskrifter](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions). Anropa [ta bort avskrift](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) regelbundet från tjänsten när du har hämtat resultatet. Alternativt kan du ange `timeToLive` egenskapen till ett rimligt värde för att säkerställa eventuell borttagning av resultaten.

## <a name="sample-code"></a>Exempelkod

Fullständiga exempel är tillgängliga i [GitHub-exempel arkivet](https://aka.ms/csspeech/samples) i under `samples/batch` katalogen.

Uppdatera exempel koden med din prenumerations information, tjänst regionen, SAS-URI: n som pekar på ljud filen för att skrivas av och modell platsen om du vill använda en anpassad modell.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Exempel koden konfigurerar klienten och skickar in avskrifts förfrågan. Den söker sedan efter statusinformation och skriver ut information om avskrifts förloppet.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Fullständig information om föregående anrop finns i vårt Swagger- [dokument](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). För det fullständiga exemplet som visas här går du till [GitHub](https://aka.ms/csspeech/samples) i under `samples/batch` katalogen.

Anteckna den asynkrona konfigurationen för att skicka ljud och ta emot avskrifts status. Klienten som du skapar är en .NET HTTP-klient. Det finns en `PostTranscriptions` metod för att skicka ljud filens information och en `GetTranscriptions` metod för att ta emot tillstånden. `PostTranscriptions`Returnerar en referens och `GetTranscriptions` använder den för att skapa en referens för att hämta avskrifts status.

Den aktuella exempel koden anger inte en anpassad modell. Tjänsten använder bas linje modellen för att skriva över filen eller filerna. Om du vill ange modellen kan du överföra modell referensen för den anpassade modellen till samma metod.

> [!NOTE]
> För bas linje avskrifter behöver du inte deklarera ID: t för bas linje modellen.

## <a name="download-the-sample"></a>Ladda ned exemplet

Du kan hitta exemplet i `samples/batch` katalogen i [GitHub-exempel lagrings platsen](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nästa steg

- [Tal till text v3 API-referens](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
