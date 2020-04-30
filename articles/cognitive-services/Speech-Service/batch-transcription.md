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
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208626"
---
# <a name="what-is-batch-transcription"></a>Vad är batch-avskriftering?

Batch-avskrift är en uppsättning REST API åtgärder som gör det möjligt att skriva av en stor mängd ljud i lagring. Du kan peka på ljudfiler med en SAS-URI (signatur för delad åtkomst) och få svars resultat asynkront.

Asynkront tal-till-text-avskrift är bara en av funktionerna. Du kan använda REST-API: er för batch-avskrift för att anropa följande metoder:



|    Batch-avskrifts åtgärd                                             |    Metod    |    REST API-anrop                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Skapar en ny avskrift.                                              |    POST      |    API/speechtotext/v 2.0/avskrifter            |
|    Hämtar en lista över avskrifter för den autentiserade prenumerationen.    |    HÄMTA       |    API/speechtotext/v 2.0/avskrifter            |
|    Hämtar en lista över språk som stöds för offline-avskrifter.              |    HÄMTA       |    API/speechtotext/v 2.0/avskrifter/nationella inställningar    |
|    Uppdaterar föränderligt-informationen för avskriften som identifieras av dess ID.    |    9.0a     |    API/speechtotext/v 2.0/avskrifter/{ID}       |
|    Tar bort den angivna avskrifts uppgiften.                                 |    DELETE    |    API/speechtotext/v 2.0/avskrifter/{ID}       |
|    Hämtar avskriften som identifieras av det angivna ID: t.                        |    HÄMTA       |    API/speechtotext/v 2.0/avskrifter/{ID}       |




Du kan granska och testa det detaljerade API: et, som är tillgängligt som ett [Swagger-dokument](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), `Custom Speech transcriptions`under rubriken.

Jobb för batch-avskrifter schemaläggs enligt bästa prestanda. Det finns för närvarande ingen uppskattning för när ett jobb ändras till körnings tillstånd. Under normal system belastning bör det ske inom några minuter. När den faktiska avskriften bearbetas fort i körnings tillstånd bearbetas den faktiska avskriften än ljudet i real tid.

Bredvid det lättanvända API: t behöver du inte distribuera anpassade slut punkter och du har inte några samtidiga krav att följa.

## <a name="prerequisites"></a>Krav

### <a name="subscription-key"></a>Prenumerationsnyckel

Precis som med alla funktioner i tal tjänsten skapar du en prenumerations nyckel från [Azure Portal](https://portal.azure.com) genom att följa vår [Guide för att komma igång](get-started.md).

>[!NOTE]
> En standard prenumeration (S0) för tal tjänst krävs för att använda batch-avskriftering. Kostnads fria prenumerations nycklar (F0) fungerar inte. Mer information finns i [priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Anpassade modeller

Om du planerar att anpassa akustiska eller språk modeller följer du stegen i [Anpassa modeller för akustisk modell](how-to-customize-acoustic-models.md) och [design anpassnings språk](how-to-customize-language-model.md). Om du vill använda de skapade modellerna i batch-avskriftering behöver du deras modell-ID. Du kan hämta modell-ID när du inspekterar informationen om modellen. Det behövs ingen distribuerad anpassad slut punkt för batch-avskrifts tjänsten.

## <a name="the-batch-transcription-api"></a>API för batch-avskriftering

### <a name="supported-formats"></a>Format som stöds

API: et för batch-avskrift stöder följande format:

| Format | ADPCM | Hastigheten | Samplings frekvens                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bitar  | 8 kHz eller 16 kHz, mono eller stereo |
| MP3    | PCM   | 16 bitar  | 8 kHz eller 16 kHz, mono eller stereo |
| OGG    | OPUS  | 16 bitar  | 8 kHz eller 16 kHz, mono eller stereo |

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

:::row:::
   :::column span="1":::
      **ProfileServiceApplicationProxy**
   :::column-end:::
   :::column span="2":::
      **Beskrivning**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Anger hur du hanterar svordomar i igenkännings resultat. Godkända värden är `None` att inaktivera filtrering av svordomar `Masked` , för att ersätta svordomar med asterisker, `Removed` för att ta bort alla svordomar från `Tags` resultatet eller för att lägga till taggar av "svordomar". Standardinställningen är `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Anger hur interpunktion ska hanteras i igenkännings resultat. Godkända värden är `None` att inaktivera interpunktion, `Dictated` för att göra det explicit (talade) skiljetecken `Automatic` att tillåta avkodaren att hantera skiljetecken eller `DictatedAndAutomatic` att använda dikterad och automatisk interpunktion. Standardinställningen är `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Anger om Word-nivåns tidsstämplar ska läggas till i utdata. Godkända värden är `true` att aktivera Word-nivåns tidsstämplar `false` och (standardvärdet) för att inaktivera det.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Anger om sentiment-analys ska tillämpas på uttryck. Godkända värden är `true` att aktivera och `false` (standardvärdet) för att inaktivera det. Se [Attitydanalys](#sentiment-analysis) för mer information.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Anger att diarization-analys ska utföras på indatamängden, vilket förväntas vara en svartvit kanal som innehåller två röster. Godkända värden `true` aktiverar diarization och `false` (standardvärdet) för att inaktivera det. Det måste `AddWordLevelTimestamps` också anges till sant.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Valfri URL med [tjänstens SAS](../../storage/common/storage-sas-overview.md) till en skrivbar behållare i Azure. Resultatet lagras i den här behållaren.
:::row-end:::

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

:::row:::
   :::column span="1":::
      **Formulär**
   :::column-end:::
   :::column span="2":::
      **Innehåll**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      De faktiska orden som identifieras.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Inverterad text – normaliserad text för den tolkade texten. Förkortningar ("läkare Smith" till "Dr Smith"), telefonnummer och andra transformeringar tillämpas.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      REDUNDANSVÄXLINGAR-formuläret med en svordom som används.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      Den tolkade textens visnings form. Tillagd interpunktion och versaler ingår.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Högtalar separation (Diarization)

Diarization är en process för att åtskilja högtalare i ett ljud. Vår batch-pipeline stöder diarization och kan identifiera två högtalare på svartvita kanal inspelningar. Funktionen är inte tillgänglig i stereo inspelningar.

Alla avskrifts utdata innehåller `SpeakerId`en. Om diarization inte används visas `"SpeakerId": null` den i JSON-utdata. För diarization har vi stöd för två röster, så att högtalarna identifieras som `"1"` eller `"2"`.

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

## <a name="sentiment-analysis"></a>Sentimentanalys

Funktionen sentiment beräknar sentiment uttryckt i ljudet. Sentiment uttrycks med ett värde mellan 0 och 1 för `Negative`, `Neutral`och `Positive` sentiment. Sentiment-analys kan till exempel användas i Call Center-scenarier:

- Få insikt om kund nöjdhet
- Få insikter om prestanda hos agenterna (team som tar emot samtal)
- Hitta den exakta tidpunkten när ett samtal tog en tur i en negativ riktning
- Vad som gick bra när du aktiverar ett negativt anrop till en positiv riktning
- Identifiera vad kunderna gillar och vad de gillar om en produkt eller en tjänst

Sentiment betygs ätt per ljud segment baserat på det lexikala formuläret. Hela texten i det ljud segmentet används för att beräkna sentiment. Ingen sammanställd sentiment beräknas för hela avskriften. Sentiment-analys är för närvarande bara tillgängligt på det engelska språket.

> [!NOTE]
> Vi rekommenderar att du använder Microsoft API för textanalys i stället. Den erbjuder mer avancerade funktioner utöver sentiment analys som extrahering av nyckel fraser, automatisk språk identifiering med mera. Du hittar information och exempel i Textanalys- [dokumentationen](https://azure.microsoft.com/services/cognitive-services/text-analytics/).
>

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

Avskrifts tjänsten kan hantera ett stort antal skickade avskrifter. Du kan fråga efter status för dina avskrifter via `GET` en på [avskrifts metoden](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Behåll informationen som returneras till en rimlig storlek genom att ange `take` parametern (några hundra). [Ta bort avskrifter](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regelbundet från tjänsten när du har hämtat resultatet. Detta garanterar snabba svar från avskrifts hanterings samtal.

## <a name="sample-code"></a>Exempelkod

Fullständiga exempel är tillgängliga i [GitHub-exempel arkivet](https://aka.ms/csspeech/samples) `samples/batch` i under katalogen.

Du måste anpassa exempel koden med din prenumerations information, tjänst regionen, SAS-URI: n som pekar på ljud filen för att skriva och modell-ID om du vill använda en anpassad akustisk eller språk modell.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Exempel koden konfigurerar klienten och skickar in avskrifts förfrågan. Den söker sedan efter statusinformation och skriver ut information om avskrifts förloppet.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Fullständig information om föregående anrop finns i vårt Swagger- [dokument](https://westus.cris.ai/swagger/ui/index). För det fullständiga exemplet som visas här går du till [GitHub](https://aka.ms/csspeech/samples) i `samples/batch` under katalogen.

Anteckna den asynkrona konfigurationen för att skicka ljud och ta emot avskrifts status. Klienten som du skapar är en .NET HTTP-klient. Det finns en `PostTranscriptions` Metod för att skicka ljud filens information och `GetTranscriptions` en metod för att ta emot resultatet. `PostTranscriptions`Returnerar en referens och `GetTranscriptions` använder den för att skapa en referens för att hämta avskrifts status.

Den aktuella exempel koden anger inte en anpassad modell. Tjänsten använder bas linje modellerna för att skriva över filen eller filerna. Om du vill ange modeller kan du skicka samma metod som modell-ID: na för ljud-och språk modellen.

> [!NOTE]
> För bas linje avskrifter behöver du inte deklarera ID: t för bas linje modeller. Om du bara anger ett språk modells-ID (och inget akustiskt modell-ID) väljs en matchande akustisk modell automatiskt. Om du bara anger ett akustiskt modell-ID väljs en matchande språk modell automatiskt.

## <a name="download-the-sample"></a>Hämta exemplet

Du kan hitta exemplet i `samples/batch` katalogen i GitHub- [exempel lagrings platsen](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
