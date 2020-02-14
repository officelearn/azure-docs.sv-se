---
title: Använda batch-avskrifter – tal tjänst
titleSuffix: Azure Cognitive Services
description: Batch avskrift är perfekt om du vill att transkribera ett stort antal ljud i lagring, till exempel Azure Blobs. Med hjälp av dedikerad REST-API kan du pekar på ljudfiler med signatur för delad åtkomst (SAS) URI och ta emot avskrifter asynkront.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: dc473c814cdd69204cddd976bc77f19b5db567b1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200086"
---
# <a name="how-to-use-batch-transcription"></a>Använda batch-avskriftering

Batch-avskrift är perfekt för att skriva över en stor mängd ljud i lagring. Genom att använda dedikerade REST API kan du peka på ljudfiler med en SAS-URI (signatur för delad åtkomst) och få svars resultaten asynkront.

API: et erbjuder asynkron avskrift av tal till text och andra funktioner. Du kan använda REST API för att Visa metoder för att:

- Skapa en grupp bearbetnings begär Anden
- Fråga efter status
- Hämta avskrifts resultat
- Ta bort avskrifts information från tjänsten

Det detaljerade API: et är tillgängligt som ett [Swagger-dokument](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)under rubriken `Custom Speech transcriptions`.

Jobb för batch-avskrifter schemaläggs enligt bästa prestanda. För närvarande finns det ingen uppskattning för när ett jobb ändras till körnings tillstånd. Under normal system belastning bör det ske inom några minuter. När den faktiska avskriften bearbetas fort i körnings tillstånd bearbetas den faktiska avskriften än ljudet i real tid.

Bredvid det lättanvända API: t behöver du inte distribuera anpassade slut punkter och du har inte några samtidiga krav att följa.

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscription-key"></a>Prenumerations nyckel

Precis som med alla funktioner i tal tjänsten skapar du en prenumerations nyckel från [Azure Portal](https://portal.azure.com) genom att följa vår [Guide för att komma igång](get-started.md).

>[!NOTE]
> En standard prenumeration (S0) för tal tjänst krävs för att använda batch-avskriftering. Kostnadsfria prenumerationsnycklar (F0) fungerar inte. Mer information finns i [priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Anpassade modeller

Om du planerar att anpassa akustiska eller språk modeller följer du stegen i [Anpassa modeller för akustisk modell](how-to-customize-acoustic-models.md) och [design anpassnings språk](how-to-customize-language-model.md). Om du vill använda de skapade modellerna i batch-avskriftering behöver du deras modell-ID. Du kan hämta modell-ID när du inspekterar informationen om modellen. Det behövs ingen distribuerad anpassad slut punkt för batch-avskrifts tjänsten.

## <a name="the-batch-transcription-api"></a>Batch-avskrift API

### <a name="supported-formats"></a>Format som stöds

API: et för Batch avskrift stöder följande format:

| Format | Codec | Bithastighet | Samplingshastighet |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bitars | 8 kHz eller 16 kHz, mono eller stereo |
| MP3-FILEN | PCM | 16-bitars | 8 kHz eller 16 kHz, mono eller stereo |
| OGG | OPUS | 16-bitars | 8 kHz eller 16 kHz, mono eller stereo |

För stereo ljud strömmar delas vänster och höger kanaler upp under avskriften. För varje kanal skapas en JSON-resultat fil. De tidsstämplar som genereras per uttryck gör att utvecklaren kan skapa en sorterad slutlig avskrift.

### <a name="configuration"></a>Konfiguration

Konfigurations parametrar tillhandahålls som JSON:

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

### <a name="configuration-properties"></a>Konfigurations egenskaper

Använd dessa valfria egenskaper för att konfigurera avskrifter:

| Parameter | Beskrivning |
|-----------|-------------|
| `ProfanityFilterMode` | Anger hur du hanterar svordomar i igenkänningsresultat. Godkända värden är `None` som inaktiverar svordomar, `Masked` som ersätter svordomar med asterisker, `Removed` som tar bort alla svordomar från resultatet eller `Tags` som lägger till "svordoms"-taggar. Standardvärdet är `Masked`. |
| `PunctuationMode` | Anger hur du hanterar skiljetecken i igenkänningsresultat. Godkända värden är `None` som inaktiverar interpunktion, `Dictated` som innebär explicit interpunktion, `Automatic` som tillåter avkodaren att hantera skiljetecken eller `DictatedAndAutomatic` som anger dikterade skiljetecken eller automatisk. |
| `AddWordLevelTimestamps` | Anger om Word-nivåns tidsstämplar ska läggas till i utdata. Godkända värden är `true` som gör att tidsstämplar på Word-nivå och `false` (standardvärdet) inaktive ras. |
| `AddSentiment` | Anger att sentiment ska läggas till i uttryck. Godkända värden är `true` som aktiverar sentiment per uttryck och `false` (standardvärdet) för att inaktivera det. |
| `AddDiarization` | Anger att diarization-analys ska utföras på indatamängden som förväntas vara en mono kanal som innehåller två röster. Godkända värden är `true` som gör det möjligt att inaktivera diarization och `false` (standardvärdet). Det kräver också att `AddWordLevelTimestamps` anges till sant.|
|`TranscriptionResultsContainerUrl`|Valfri URL med [tjänstens SAS](../../storage/common/storage-sas-overview.md) till en skrivbar behållare i Azure. Resultatet kommer att lagras i den här behållaren.

### <a name="storage"></a>Storage

Batch-avskrift stöder [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) för att läsa ljud och skriva avskrifter till lagring.

## <a name="the-batch-transcription-result"></a>Resultatet av batch-avskriften

För svartvita indata skapas en resultat fil för ett avskrifts resultat. För ljud för stereo indata skapas två avskrifts resultat. Var och en har den här strukturen:

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

|Typ|Innehåll|
|-|-|
|`Lexical`|De faktiska orden som identifieras.
|`ITN`|Inverterad text – normaliserad text för den tolkade texten. Förkortningar ("läkare Smith" till "Dr Smith"), telefonnummer och andra transformeringar tillämpas.
|`MaskedITN`|REDUNDANSVÄXLINGAR-formuläret med en svordom som används.
|`Display`|Den tolkade textens visnings form. Detta inkluderar tillagd interpunktion och Skift läge.

## <a name="speaker-separation-diarization"></a>Högtalar separation (Diarization)

Diarization är en process för att åtskilja högtalare i ett ljud. Vår batch-pipeline stöder diarization och kan identifiera två högtalare på svartvita kanal inspelningar. Funktionen är inte tillgänglig i stereo inspelningar.

Alla avskrifts utdata innehåller en `SpeakerId`. Om diarization inte används visas `"SpeakerId": null` i JSON-utdata. För diarization har vi stöd för två röster, så att högtalarna identifieras som `"1"` eller `"2"`.

Om du vill begära diarization behöver du bara lägga till relevant parameter i HTTP-begäran som visas nedan.

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

Tidsstämplar på Word-nivå måste också vara "aktiverat" eftersom parametrarna i ovanstående begäran visar.

## <a name="sentiment-analysis"></a>Attitydanalys

Funktionen sentiment beräknar sentiment uttryckt i ljudet. Sentiment uttrycks med ett värde mellan 0 och 1 för `Negative`, `Neutral`och `Positive` sentiment. Sentiment-analys kan till exempel användas i Call Center-scenarier:

- Få insikt om kund nöjdhet
- Få insikter om prestanda hos agenterna (team som tar emot samtal)
- Hitta den exakta tidpunkten när ett samtal tog en tur i en negativ riktning
- Vad som gick bra när du aktiverar ett negativt anrop till en positiv riktning
- Identifiera vad kunderna gillar och vad de gillar om en produkt eller en tjänst

Sentiment betygs ätt per ljud segment baserat på det lexikala formuläret. Hela texten i det ljud segmentet används för att beräkna sentiment. Ingen sammanställd sentiment beräknas för hela avskriften.

Exempel på JSON-utdata ser ut så här:

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

Avskrifts tjänsten kan hantera ett stort antal skickade avskrifter. Du kan fråga efter status för dina avskrifter genom en `GET` i [avskrifts metoden](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Behåll informationen som returneras till en rimlig storlek genom att ange `take` parameter (några hundra). [Ta bort avskrifter](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regelbundet från tjänsten när du har hämtat resultatet. Detta garanterar snabba svar från avskrifts hanterings samtal.

## <a name="sample-code"></a>Exempelkod

Fullständiga exempel är tillgängliga i [GitHub-lagringsplatsen](https://aka.ms/csspeech/samples) i under katalogen `samples/batch`.

Du måste anpassa exempel koden med din prenumerations information, tjänst regionen, SAS-URI: n som pekar på ljud filen för att skriva och modell-ID om du vill använda en anpassad akustisk eller språk modell.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Exempel koden konfigurerar klienten och skickar in avskrifts förfrågan. Den söker sedan efter statusinformation och skriver ut information om avskrifts förloppet.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Fullständig information om föregående anrop finns i vårt Swagger- [dokument](https://westus.cris.ai/swagger/ui/index). För det fullständiga exemplet som visas här går du till [GitHub](https://aka.ms/csspeech/samples) i under katalogen `samples/batch`.

Anteckna asynkron konfigurationen för att skicka ljud och ta emot avskrift status. Klienten som du skapar är en .NET-HTTP-klient. Det finns en `PostTranscriptions` metod för att skicka ljud filens information och en `GetTranscriptions` metod för att ta emot resultatet. `PostTranscriptions` returnerar en referens och `GetTranscriptions` använder den för att skapa en referens för att hämta avskrifts statusen.

Aktuella exempelkoden Ange inte en anpassad modell. Tjänsten använder baslinjemodeller för att skriva av den filen eller filerna. Om du vill ange modeller, kan du skicka på samma metod som modell-ID för akustiska och språkmodellen.

> [!NOTE]
> För bas linje avskrifter behöver du inte deklarera ID: t för bas linje modeller. Om du bara anger ett språk modells-ID (och inget akustiskt modell-ID) väljs en matchande akustisk modell automatiskt. Om du bara anger ett akustiskt modell-ID väljs en matchande språk modell automatiskt.

## <a name="download-the-sample"></a>Hämta exemplet

Du hittar exemplet i `samples/batch`-katalogen i [GitHub-exempel lagrings platsen](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
