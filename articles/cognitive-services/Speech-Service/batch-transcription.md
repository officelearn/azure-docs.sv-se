---
title: Använda batch-avskrifter – tal tjänst
titleSuffix: Azure Cognitive Services
description: Batch-avskrift är idealiskt om du vill skriva över en stor mängd ljud i lagring, till exempel Azure-blobbar. Genom att använda den dedikerade REST API kan du peka på ljudfiler med en SAS-URI (signatur för delad åtkomst) och få asynkront erhålla avskrifter.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 158a99b1691e59fa58207f3c9291ca9d37a6679c
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538120"
---
# <a name="why-use-batch-transcription"></a>Varför ska jag använda batch-avskriftering?

Batch-avskrift är idealiskt om du vill skriva över en stor mängd ljud i lagring, till exempel Azure-blobbar. Genom att använda den dedikerade REST API kan du peka på ljudfiler med en SAS-URI (signatur för delad åtkomst) och få asynkront erhålla avskrifter.

## <a name="prerequisites"></a>Krav

### <a name="subscription-key"></a>Prenumerations nyckel

Precis som med alla funktioner i tal tjänsten skapar du en prenumerations nyckel från [Azure Portal](https://portal.azure.com) genom att följa vår [Guide för att komma igång](get-started.md). Om du planerar att få avskrifter från våra bas linje modeller behöver du bara skapa en nyckel.

>[!NOTE]
> En standard prenumeration (S0) för tal tjänster krävs för att använda batch-avskriftering. Kostnads fria prenumerations nycklar (F0) fungerar inte. Mer information finns i [priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Anpassade modeller

Om du planerar att anpassa akustiska eller språk modeller följer du stegen i [Anpassa akustiska modeller](how-to-customize-acoustic-models.md) och [Anpassa språk modeller](how-to-customize-language-model.md). Om du vill använda de skapade modellerna i batch-avskriftering behöver du deras modell-ID. Detta ID är inte det slut punkts-ID som du hittar i vyn information om slut punkt, det är det modell-ID som du kan hämta när du väljer information om modellerna.

## <a name="the-batch-transcription-api"></a>API för batch-avskriftering

API: et för batch-avskrift erbjuder asynkron avskrift för tal till text, tillsammans med ytterligare funktioner. Det är ett REST API som visar metoder för:

1. Skapar begär Anden om batchbearbetning
1. Status för fråga
1. Laddar ned avskrifter

> [!NOTE]
> API: et för batch-avskrift är idealiskt för Call Center, som vanligt vis ackumulerar tusentals timmars ljud. Det gör det enkelt att skriva över stora volymer med ljud inspelningar.

### <a name="supported-formats"></a>Format som stöds

API: et för batch-avskrift stöder följande format:

| Format | ADPCM | Hastigheten | Samplings frekvens |
|--------|-------|---------|-------------|
| VÅG | PCM | 16-bitars | 8 eller 16 kHz, mono, stereo |
| MP3 | PCM | 16-bitars | 8 eller 16 kHz, mono, stereo |
| OGG | OPUS | 16-bitars | 8 eller 16 kHz, mono, stereo |

För stereo ljud strömmar delar API: et för batch-avskrift den vänstra och högra kanalen under avskriften. De två JSON-filerna med resultatet är de som skapas från en enda kanal. Tidsstämplar per uttryck gör att utvecklaren kan skapa en sorterad slutlig avskrift. Den här exempel förfrågan innehåller egenskaper för tids inställningar för filtrering, interpunktion och Word-nivå för svordomar.

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
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> API: et för batch-avskrift använder en REST-tjänst för att begära avskrifter, deras status och tillhör ande resultat. Du kan använda API: et från valfritt språk. I nästa avsnitt beskrivs hur API: et används.

### <a name="configuration-properties"></a>Konfigurations egenskaper

Använd dessa valfria egenskaper för att konfigurera avskrifter:

| Parameter | Beskrivning |
|-----------|-------------|
| `ProfanityFilterMode` | Anger hur du hanterar svordomar i igenkännings resultat. Godkända värden är `None` som inaktiverar svordomar, `masked` som ersätter svordomar med asterisker, `removed` som tar bort alla svordomar från resultatet eller `tags` som lägger till "svordoms"-taggar. Standardvärdet är `masked`. |
| `PunctuationMode` | Anger hur interpunktion ska hanteras i igenkännings resultat. Godkända värden är `None` som inaktiverar interpunktion, `dictated` som innebär explicit interpunktion, `automatic` som tillåter avkodaren att hantera skiljetecken eller `dictatedandautomatic` som anger dikterade skiljetecken eller automatisk. |
 | `AddWordLevelTimestamps` | Anger om Word-nivåns tidsstämplar ska läggas till i utdata. Godkända värden är `true` som gör att tidsstämplar på Word-nivå och `false` (standardvärdet) inaktive ras. |
 | `AddSentiment` | Anger att sentiment ska läggas till i uttryck. Godkända värden är `true` som aktiverar sentiment per uttryck och `false` (standardvärdet) för att inaktivera det. |
 | `AddDiarization` | Anger att diarization-analys ska utföras på indatamängden som förväntas vara en mono kanal som innehåller två röster. Godkända värden är `true` som gör det möjligt att inaktivera diarization och `false` (standardvärdet). Det kräver också att `AddWordLevelTimestamps` anges till sant.|

### <a name="storage"></a>Lagring

Batch-avskrift stöder [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) för att läsa ljud och skriva avskrifter till lagring.

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

Observera också att Diarization inte är tillgängligt i stereo inspelningar. Dessutom kommer alla JSON-utdata att innehålla en högtalar tagg. Om diarization inte används visas "talare: null" i JSON-utdata.

> [!NOTE]
> Diarization är tillgängligt i alla regioner och för alla språk!

## <a name="sentiment"></a>Sentiment

Sentiment är en ny funktion i API för batch-avskrift och är en viktig funktion i Call Center-domänen. Kunder kan använda `AddSentiment` parametrar för att begära

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

Fullständiga exempel är tillgängliga i [GitHub-lagringsplatsen](https://aka.ms/csspeech/samples) i under katalogen `samples/batch`.

Du måste anpassa exempel koden med din prenumerations information, tjänst regionen, SAS-URI: n som pekar på ljud filen för att skriva och modell-ID om du vill använda en anpassad akustisk eller språk modell.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Exempel koden konfigurerar klienten och skickar in avskrifts förfrågan. Den söker sedan efter statusinformation och skriver ut information om avskrifts förloppet.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Fullständig information om föregående anrop finns i vårt Swagger- [dokument](https://westus.cris.ai/swagger/ui/index). För det fullständiga exemplet som visas här går du till [GitHub](https://aka.ms/csspeech/samples) i under katalogen `samples/batch`.

Anteckna den asynkrona konfigurationen för att skicka ljud och ta emot avskrifts status. Klienten som du skapar är en .NET HTTP-klient. Det finns en `PostTranscriptions` metod för att skicka ljud filens information och en `GetTranscriptions` metod för att ta emot resultatet. `PostTranscriptions` returnerar en referens och `GetTranscriptions` använder den för att skapa en referens för att hämta avskrifts statusen.

Den aktuella exempel koden anger inte en anpassad modell. Tjänsten använder bas linje modellerna för att skriva över filen eller filerna. Om du vill ange modeller kan du skicka samma metod som modell-ID: na för ljud-och språk modellen.

> [!NOTE]
> För bas linje avskrifter behöver du inte deklarera ID: t för bas linje modeller. Om du bara anger ett språk modells-ID (och inget akustiskt modell-ID) väljs en matchande akustisk modell automatiskt. Om du bara anger ett akustiskt modell-ID väljs en matchande språk modell automatiskt.

## <a name="download-the-sample"></a>Hämta exemplet

Du hittar exemplet i `samples/batch`-katalogen i [GitHub-exempel lagrings platsen](https://aka.ms/csspeech/samples).

> [!NOTE]
> Batch-avskrifts jobb schemaläggs på bästa möjliga sätt, det finns ingen tids uppskattning för när ett jobb ska ändras till körnings tillstånd. När den faktiska avskriften bearbetas fort i körnings tillstånd bearbetas den faktiska avskriften än ljudet i real tid.

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
