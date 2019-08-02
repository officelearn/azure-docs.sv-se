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
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 088b6ef93631cb964979de3621453caa430c5b1e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559696"
---
# <a name="why-use-batch-transcription"></a>Varför använda Batch avskrift?

Batch avskrift är perfekt om du vill att transkribera ett stort antal ljud i lagring, till exempel Azure Blobs. Med hjälp av dedikerad REST-API kan du pekar på ljudfiler med signatur för delad åtkomst (SAS) URI och ta emot avskrifter asynkront.

## <a name="prerequisites"></a>Förutsättningar

### <a name="subscription-key"></a>Prenumerationsnyckel

Som med alla funktioner i Speech-tjänsten skapar du en prenumerationsnyckel från den [Azure-portalen](https://portal.azure.com) genom att följa våra [startguide](get-started.md). Om du planerar att hämta avskrifter från våra basmodeller, är skapar en nyckel allt du behöver göra.

>[!NOTE]
> En standard-prenumerationen (S0) för Speech Services krävs för att använda batch avskrift. Kostnadsfria prenumerationsnycklar (F0) fungerar inte. Mer information finns i [priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Anpassade modeller

Om du planerar att anpassa akustiska eller språk modeller följer du stegen i [Anpassa akustiska modeller](how-to-customize-acoustic-models.md) och [Anpassa språk modeller](how-to-customize-language-model.md). Om du vill använda de skapade modellerna i batch-avskriftering behöver du deras modell-ID. Detta ID är inte det slut punkts-ID som du hittar i vyn information om slut punkt, det är det modell-ID som du kan hämta när du väljer information om modellerna.

## <a name="the-batch-transcription-api"></a>Batch-avskrift API

API: et för Batch avskrift erbjuder asynkron tal till text-avskrift, tillsammans med ytterligare funktioner. Det är en REST-API som exponerar metoder för att:

1. Skapar batch-bearbetning av begäranden
1. Frågestatus
1. Ladda ned avskrifter

> [!NOTE]
> API: et för Batch avskrift är perfekt för call Center, som vanligtvis ackumuleras tusentals timmars ljud. Det gör det enkelt att skriva över stora volymer med ljud inspelningar.

### <a name="supported-formats"></a>Format som stöds

API: et för Batch avskrift stöder följande format:

| Format | Codec | Bithastighet | Samplingshastighet |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bitars | 8 eller 16 kHz, mono, stereo |
| MP3-FILEN | PCM | 16-bitars | 8 eller 16 kHz, mono, stereo |
| OGG | OPUS | 16-bitars | 8 eller 16 kHz, mono, stereo |

Delar upp kanalen vänster och höger under utskrift för stereo ljudströmmar Batch avskrift API. De två JSON-filerna med resultatet skapas var och en från en enda kanal. Tidsstämplar per uttryck gör att utvecklare kan skapa en ordnad slutlig avskrift. Den här exempel förfrågan innehåller egenskaper för tids inställningar för filtrering, interpunktion och Word-nivå för svordomar.

### <a name="configuration"></a>Konfiguration

Konfigurations parametrar tillhandahålls som JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> API för Batch-avskrift använder en REST-tjänst för att begära avskrifter, deras status och associerade resultat. Du kan använda API: T från alla språk. I nästa avsnitt beskrivs hur API: et används.

### <a name="configuration-properties"></a>Konfigurations egenskaper

Använd dessa valfria egenskaper för att konfigurera avskrifter:

| Parameter | Beskrivning |
|-----------|-------------|
| `ProfanityFilterMode` | Anger hur du hanterar svordomar i igenkänningsresultat. Godkända värden är `none` som inaktiverar svordomar filtrering, `masked` som ersätter svordomar med asterisker `removed` som tar bort alla svordomar från resultatet, eller `tags` som lägger till ”svordomar”-taggar. Standardinställningen är `masked`. |
| `PunctuationMode` | Anger hur du hanterar skiljetecken i igenkänningsresultat. Godkända värden är `none` som inaktiverar skiljetecken, `dictated` vilket medför att explicit skiljetecken `automatic` som gör att avkodaren handlar om skiljetecken, eller `dictatedandautomatic` vilket medför processens skiljetecken eller automatiskt. |
 | `AddWordLevelTimestamps` | Anger om Word-nivåns tidsstämplar ska läggas till i utdata. Godkända värden är `true` som aktiverar Word-nivåns tidsstämplar `false` och (standardvärdet) för att inaktivera det. |
 | `AddSentiment` | Anger att sentiment ska läggas till i uttryck. Godkända värden är `true` som aktiverar sentiment per uttryck och `false` (standardvärdet) för att inaktivera det. |
 | `AddDiarization` | Anger att diarization alalysis ska utföras på inaktuella indatatyper, vilket förväntas vara en svartvit kanal som innehåller två röster. Godkända värden är `true` som aktiverar diarization och `false` (standardvärdet) för att inaktivera det. Det måste `AddWordLevelTimestamps` också anges till sant.|

### <a name="storage"></a>Storage

Batch-avskrift stöder [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) för att läsa ljud och skriva avskrifter till lagring.

## <a name="webhooks"></a>Webhook-konfigurationer

Avsökningen efter avskrifts status kanske inte är den mest presterande eller ger den bästa användar upplevelsen. Om du vill avsöka efter status kan du registrera återanrop som meddelar klienten när tids krävande avskrifts uppgifter har slutförts.

Mer information finns i Webhooks. [](webhooks.md)

## <a name="speaker-separation-diarization"></a>Högtalar separation (Diarization)

Diarization är en process för att åtskilja högtalare i ett ljud. Vår batch-pipeline stöder Diarization och kan identifiera två högtalare på svartvita kanal inspelningar.

Om du vill begära att din begäran om ljud avskrift bearbetas för diarization, behöver du bara lägga till den relevanta parametern i HTTP-förfrågan enligt vad som visas nedan.

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

Motsvarande ljud innehåller de högtalare som identifieras av ett tal (för närvarande endast stöd för två röster, så att högtalarna identifieras som "högtalare 1" och "högtalare 2") följt av avskrifts resultatet.

Observera också att Diarization inte är tillgängligt i stereo inspelningar. Dessutom kommer alla JSON-utdata att innehålla en högtalar tagg. Om diarization inte används visas "talare: Null i JSON-utdata.

> [!NOTE]
> Diarization är tillgängligt i alla regioner och för alla språk!

## <a name="sentiment"></a>Sentiment

Sentiment är en ny funktion i API för batch-avskrift och är en viktig funktion i Call Center-domänen. Kunder kan använda `AddSentiment` parametrarna för sina förfrågningar för att

1.  Få insikter om kund nöjdhet
2.  Få insikter om prestanda hos agenterna (team som tar emot samtal)
3.  Hitta den exakta tidpunkten när ett anrop tog en tur i en negativ riktning
4.  Hitta det som gick bra när du inaktiverar negativa anrop till positiv
5.  Identifiera vad kunderna gillar och vad de gillar om en produkt eller en tjänst

Sentiment betygs ätt per ljud segment där ett ljud segment definieras som tids fördröjning mellan starten av uttryck (förskjutning) och identifierings tystnaden för slutet av byte-dataströmmen. Hela texten i segmentet används för att beräkna sentiment. Vi beräknar inte några sammanställda sentiment-värden för hela anropet eller hela talet för varje kanal. Dessa agg regeringar lämnas till domänens ägare för att ytterligare gälla.

Sentiment används i det lexikala formuläret.

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
Funktionen använder en sentiment-modell som för närvarande är en beta version.

## <a name="sample-code"></a>Exempelkod

Fullständiga exempel är tillgängliga i [GitHub-exempel arkivet](https://aka.ms/csspeech/samples) `samples/batch` i under katalogen.

Du måste anpassa exempel koden med din prenumerations information, tjänst regionen, SAS-URI: n som pekar på ljud filen för att skriva och modell-ID om du vill använda en anpassad akustisk eller språk modell.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Exempel koden kommer att konfigurera klienten och skicka in avskrifts förfrågan. Den söker sedan efter statusinformation och skriver ut information om avskrifts förloppet.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Fullständig information om föregående anrop finns i vårt Swagger- [dokument](https://westus.cris.ai/swagger/ui/index). För det fullständiga exemplet som visas här går du till [GitHub](https://aka.ms/csspeech/samples) i `samples/batch` under katalogen.

Anteckna asynkron konfigurationen för att skicka ljud och ta emot avskrift status. Klienten som du skapar är en .NET-HTTP-klient. Det finns en `PostTranscriptions` metod för att skicka ljud Filinformation och en `GetTranscriptions` metod för att ta emot resultaten. `PostTranscriptions` Returnerar en referens och `GetTranscriptions` används för att skapa en referens för att hämta status för avskrift.

Aktuella exempelkoden Ange inte en anpassad modell. Tjänsten använder baslinjemodeller för att skriva av den filen eller filerna. Om du vill ange modeller, kan du skicka på samma metod som modell-ID för akustiska och språkmodellen.

> [!NOTE]
> För bas linje avskrifter behöver du inte deklarera ID: t för bas linje modeller. Om du bara anger ett språk modells-ID (och inget akustiskt modell-ID) väljs en matchande akustisk modell automatiskt. Om du bara anger ett akustiskt modell-ID väljs en matchande språk modell automatiskt.

## <a name="download-the-sample"></a>Hämta exemplet

Du kan hitta exemplet i `samples/batch` katalogen i GitHub- [exempel lagrings platsen](https://aka.ms/csspeech/samples).

> [!NOTE]
> Batch-avskrifts jobb schemaläggs på bästa möjliga sätt, det finns ingen tids uppskattning för när ett jobb ska ändras till körnings tillstånd. När den faktiska avskriften bearbetas fort i körnings tillstånd bearbetas den faktiska avskriften än ljudet i real tid.

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
