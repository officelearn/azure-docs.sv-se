---
title: Vad är batch transkription - Taltjänst
titleSuffix: Azure Cognitive Services
description: Batch transkription är perfekt om du vill transkribera en stor mängd ljud i lagring, till exempel Azure Blobbar. Genom att använda det dedikerade REST API:et kan du peka på ljudfiler med en SAS-signatur (Shared Access Signature) och asynkront ta emot transkriptioner.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: fb39f1ec83416ee8ab2a33b514971110db0c0b17
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668839"
---
# <a name="what-is-batch-transcription"></a>Vad är batch transkription?

Batch transkription är en uppsättning REST API-åtgärder som gör att du kan transkribera en stor mängd ljud i lagring. Du kan peka på ljudfiler med en SAS-signatur (Shared Access Signature) och asynkront ta emot transkriptionsresultat.

Asynkron tal-till-text transkription är bara en av funktionerna. Du kan använda klotranskription-REST-API:er för att anropa följande metoder:



|    Batch transkription Operation                                             |    Metod    |    REST API-anrop                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Skapar en ny transkription.                                              |    POST      |    api/speechtotext/v2.0/transkriptioner            |
|    Hämtar en lista över transkriptioner för den autentiserade prenumerationen.    |    HÄMTA       |    api/speechtotext/v2.0/transkriptioner            |
|    Hämtar en lista över språk som stöds för offline transkriptioner.              |    HÄMTA       |    api/speechtotext/v2.0/transkriptioner/språk    |
|    Uppdaterar de föränderliga detaljerna i transkriptionen som identifieras av dess ID.    |    Patch     |    api/speechtotext/v2.0/transkriptioner/{id}       |
|    Tar bort den angivna transkriptionsuppgiften.                                 |    DELETE    |    api/speechtotext/v2.0/transkriptioner/{id}       |
|    Får transkriptionen identifierad av det angivna ID:t.                        |    HÄMTA       |    api/speechtotext/v2.0/transkriptioner/{id}       |




Du kan granska och testa det detaljerade API:et, som `Custom Speech transcriptions`är tillgängligt som ett [Swagger-dokument](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)under rubriken .

Batch transkription jobb är schemalagda på bästa sätt. För närvarande finns det ingen uppskattning för när ett jobb ändras till körtillståndet. Under normal systembelastning bör det ske inom några minuter. En gång i körtillstånd bearbetas den faktiska transkriptionen snabbare än ljudet i realtid.

Bredvid det lättanvända API:et behöver du inte distribuera anpassade slutpunkter och du har inga samtidighetskrav att följa.

## <a name="prerequisites"></a>Krav

### <a name="subscription-key"></a>Prenumerationsnyckel

Precis som med alla funktioner i taltjänsten skapar du en prenumerationsnyckel från [Azure-portalen](https://portal.azure.com) genom att följa vår [Komma igång-guide](get-started.md).

>[!NOTE]
> En standardprenumeration (S0) för Tal-tjänsten krävs för att använda batchdeskription. Gratis prenumerationsnycklar (F0) fungerar inte. Mer information finns i [priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Anpassade modeller

Om du planerar att anpassa akustiska modeller eller språkmodeller följer du stegen i [Anpassa akustiska modeller](how-to-customize-acoustic-models.md) och [språkmodeller för designanpassning](how-to-customize-language-model.md). Om du vill använda de skapade modellerna i batchranskription behöver du deras modell-ID. Du kan hämta modell-ID:t när du kontrollerar modellens information. En distribuerad anpassad slutpunkt behövs inte för batchavskriftningstjänsten.

## <a name="the-batch-transcription-api"></a>Api:et för batch-transkription

### <a name="supported-formats"></a>Format som stöds

Api:et för batchavskrifter stöder följande format:

| Format | Codec | Bitrate | Exempelhastighet                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bitar  | 8 kHz eller 16 kHz, mono eller stereo |
| Mp3    | PCM   | 16 bitar  | 8 kHz eller 16 kHz, mono eller stereo |
| Ogg    | Opus  | 16 bitar  | 8 kHz eller 16 kHz, mono eller stereo |

För stereoljudströmmar delas vänster och höger kanal under transkriptionen. För varje kanal skapas en JSON-resultatfil. Tidsstämplarna som genereras per uttryck gör det möjligt för utvecklaren att skapa en ordnad slutlig avskrift.

### <a name="configuration"></a>Konfiguration

Konfigurationsparametrar tillhandahålls som JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Konfigurationsegenskaper

Använd de här valfria egenskaperna för att konfigurera transkription:

:::row:::
   :::column span="1":::
      **Parametern**
   :::column-end:::
   :::column span="2":::
      **Beskrivning**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Anger hur svordomar ska hanteras i igenkänningsresultat. Godkända värden `None` är att inaktivera svordomar `Masked` filtrering, att ersätta svordomar `Removed` med asterisker, att ta `Tags` bort alla svordomar från resultatet, eller att lägga till "svordomar" taggar. Standardinställningen är `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Anger hur skiljetecken ska hanteras i igenkänningsresultat. Godkända värden `None` är att inaktivera `Dictated` interpunktion, att antyda explicit `Automatic` (talat) skiljetecken, att låta `DictatedAndAutomatic` avkodaren hantera skiljetecken, eller att använda dikterade och automatiska skiljetecken. Standardinställningen är `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Anger om tidsstämplar på ordnivå ska läggas till i utdata. Godkända värden `true` är att aktivera tidsstämplar på ordnivå och `false` (standardvärdet) för att inaktivera den.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Anger om sentimentanalys ska tillämpas på uttrycket. Godkända värden `true` är `false` att aktivera och (standardvärdet) för att inaktivera den.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Anger att diariseringsanalys ska utföras på indata, som förväntas vara monokanal som innehåller två röster. Godkända värden `true` aktiverar diarisering `false` och (standardvärdet) för att inaktivera den. Det måste `AddWordLevelTimestamps` också ställas in på sant.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Valfri URL med [service SAS](../../storage/common/storage-sas-overview.md) till en skrivbar behållare i Azure. Resultatet lagras i den här behållaren.
:::row-end:::

### <a name="storage"></a>Storage

Batch transkription stöder [Azure Blob lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) för att läsa ljud och skriva transkriptioner till lagring.

## <a name="the-batch-transcription-result"></a>Resultatet av batchutskriften

För mono inmatningsljud skapas en transkriptionsresultatfil. För stereoinmatningsljud skapas två transkriptionsresultatfiler. Var och en har denna struktur:

```json
{
  "AudioFileResults":[
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Resultatet innehåller följande formulär:

| Formulär        | Innehåll                                                                                                                                                  |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Lexical`   | De faktiska orden känns igen.                                                                                                                             |
| `ITN`       | Omvänd text-normaliserad form av den tolkade texten. Förkortningar ("doctor smith" till "dr smith"), telefonnummer och andra omvandlingar tillämpas. |
| `MaskedITN` | ITN-formen med svordomar maskering tillämpas.                                                                                                             |
| `Display`   | Visningsformen för den tolkade texten. Tillagd interpunktion och versaler ingår.                                                             |

## <a name="speaker-separation-diarization"></a>Avskiljande av högtalare (Diarization)

Diarization är processen att separera högtalare i en bit ljud. Vår Batch pipeline stöder diarization och kan känna igen två högtalare på mono kanal inspelningar. Funktionen är inte tillgänglig på stereoinspelningar.

All transkriptionsutdata `SpeakerId`innehåller en . Om diarisering inte används `"SpeakerId": null` visas den i JSON-utdata. För diarization stöder vi två röster, `"1"` så `"2"`högtalarna identifieras som eller .

Om du vill begära diarisering måste du helt enkelt lägga till den relevanta parametern i HTTP-begäran enligt nedan.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Tidsstämplar på Word-nivå måste också vara "aktiverade" som parametrarna i ovanstående begäran anger.

## <a name="sentiment-analysis"></a>Sentimentanalys

Sentimentfunktionen uppskattar känslan som uttrycks i ljudet. Sentimentet uttrycks av ett värde mellan `Negative`0 och 1 för , `Neutral`och `Positive` sentiment. Sentimentanalys kan till exempel användas i scenarier för kundtjänster:

- Få insikt om kundnöjdhet
- Få insikt om agenternas prestanda (team tar samtalen)
- Hitta den exakta tidpunkten när ett samtal tog en sväng i negativ riktning
- Vad gick bra när man vände ett negativt samtal till en positiv riktning
- Identifiera vad kunderna gillar och vad de ogillar med en produkt eller en tjänst

Sentimentet poängsätts per ljudsegment baserat på den lexikala formen. Hela texten inom det ljudsegmentet används för att beräkna sentiment. Ingen aggregerad sentiment beräknas för hela transkriptionen. För närvarande sentimentanalys är endast tillgänglig för det engelska språket.

Ett JSON-utdataexempel ser ut nedan:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>Bästa praxis

Transkriptionstjänsten kan hantera ett stort antal inlämnade transkriptioner. Du kan fråga status för dina `GET` transkriptioner genom en på [transkriptionsmetoden](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Håll informationen returnerad till en rimlig `take` storlek genom att ange parametern (några hundra). [Ta bort transkriptioner](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regelbundet från tjänsten när du har hämtat resultaten. Detta garanterar snabba svar från transkriptionens hanteringsanrop.

## <a name="sample-code"></a>Exempelkod

Fullständiga exempel är tillgängliga i [GitHub-exempeldatabasen](https://aka.ms/csspeech/samples) i underkatalogen. `samples/batch`

Du måste anpassa exempelkoden med din prenumerationsinformation, tjänstregionen, SAS URI som pekar på ljudfilen för att transkribera och modell-ID om du vill använda en anpassad akustisk modell eller språkmodell.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Exempelkoden ställer in klienten och skickar transkriptionsbegäran. Den söker sedan efter statusinformation och skriver ut information om transkriptionsförloppet.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

För fullständig information om föregående samtal, se vårt [Swagger-dokument](https://westus.cris.ai/swagger/ui/index). För hela exemplet som visas här, gå `samples/batch` till [GitHub](https://aka.ms/csspeech/samples) i underkatalogen.

Ta del av den asynkrona inställningen för att publicera ljud och ta emot transkriptionsstatus. Klienten som du skapar är en HTTP-klient för .NET. Det finns `PostTranscriptions` en metod för att skicka `GetTranscriptions` information om ljudfiler och en metod för att ta emot resultaten. `PostTranscriptions`returnerar en `GetTranscriptions` handtag och använder den för att skapa en handtag för att få transkriptionsstatus.

Den aktuella exempelkoden anger inte en anpassad modell. Tjänsten använder baslinjemodellerna för att transkribera filen eller filerna. Om du vill ange modellerna kan du vidarebefordra samma metod som modell-ID:erna för den akustiska och språkmodellen.

> [!NOTE]
> För baslinjeranskriptioner behöver du inte deklarera ID:t för baslinjemodellerna. Om du bara anger ett språkmodell-ID (och inget akustiskt modell-ID) väljs en matchande akustisk modell automatiskt. Om du bara anger ett akustiskt modell-ID väljs en matchande språkmodell automatiskt.

## <a name="download-the-sample"></a>Hämta exemplet

Du hittar exemplet i `samples/batch` katalogen i [GitHub-exempeldatabasen](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
