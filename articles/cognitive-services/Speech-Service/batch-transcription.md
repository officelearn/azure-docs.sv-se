---
title: Hur du använder Batch avskrift – Speech Services
titlesuffix: Azure Cognitive Services
description: Batch avskrift är perfekt om du vill att transkribera ett stort antal ljud i lagring, till exempel Azure Blobs. Med hjälp av dedikerad REST-API kan du pekar på ljudfiler med signatur för delad åtkomst (SAS) URI och ta emot avskrifter asynkront.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b389d86fe4d23e3f4ee1c66e4270a74351098129
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059613"
---
# <a name="why-use-batch-transcription"></a>Varför använda Batch avskrift?

Batch avskrift är perfekt om du vill att transkribera ett stort antal ljud i lagring, till exempel Azure Blobs. Med hjälp av dedikerad REST-API kan du pekar på ljudfiler med signatur för delad åtkomst (SAS) URI och ta emot avskrifter asynkront.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="subscription-key"></a>Prenumerationsnyckel

Som med alla funktioner i Speech-tjänsten skapar du en prenumerationsnyckel från den [Azure-portalen](https://portal.azure.com) genom att följa våra [startguide](get-started.md). Om du planerar att hämta avskrifter från våra basmodeller, är skapar en nyckel allt du behöver göra.

>[!NOTE]
> En standard-prenumerationen (S0) för Speech Services krävs för att använda batch avskrift. Kostnadsfria prenumerationsnycklar (F0) fungerar inte. Mer information finns i [priser och begränsningar](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Anpassade modeller

Om du planerar att anpassa akustiska eller språk modeller, följer du stegen i [anpassa akustiska modeller](how-to-customize-acoustic-models.md) och [anpassa språkmodeller](how-to-customize-language-model.md). Om du vill använda de skapade modellerna i batch avskrift måste deras modell-ID: N. Detta ID är inte slutpunkts-ID som du hittar i vyn information om slutpunkten, det är det modell-ID som du kan hämta när du väljer information om modeller.

## <a name="the-batch-transcription-api"></a>Batch-avskrift API

API: et för Batch avskrift erbjuder asynkron tal till text-avskrift, tillsammans med ytterligare funktioner. Det är en REST-API som exponerar metoder för att:

1. Skapar batch-bearbetning av begäranden
1. Frågestatus
1. Ladda ned avskrifter

> [!NOTE]
> API: et för Batch avskrift är perfekt för call Center, som vanligtvis ackumuleras tusentals timmars ljud. Det gör det enkelt att transkribera stora mängder ljudinspelningar.

### <a name="supported-formats"></a>Format som stöds

API: et för Batch avskrift stöder följande format:

| Format | Codec | Bithastighet | Samplingshastighet |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bitars | 8 eller 16 kHz, mono, stereo |
| MP3-FILEN | PCM | 16-bitars | 8 eller 16 kHz, mono, stereo |
| OGG | OPUS | 16-bitars | 8 eller 16 kHz, mono, stereo |

Delar upp kanalen vänster och höger under utskrift för stereo ljudströmmar Batch avskrift API. De två JSON-filerna med resultatet skapas var och en från en enda kanal. Tidsstämplar per uttryck gör att utvecklare kan skapa en ordnad slutlig avskrift. Den här exempelförfrågan innehåller egenskaper för svordomar filtrering, interpunktion och word på tidsstämplar. 

### <a name="configuration"></a>Konfiguration

Konfigurationsparametrar tillhandahålls som JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": ["<optional acoustic model ID>, <optional language model ID>"],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  }
}
```

> [!NOTE]
> API för Batch-avskrift använder en REST-tjänst för att begära avskrifter, deras status och associerade resultat. Du kan använda API: T från alla språk. I nästa avsnitt beskrivs hur API: et används.

### <a name="configuration-properties"></a>Konfigurationsegenskaper

| Parameter | Beskrivning | Obligatoriskt / valfritt |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Anger hur du hanterar svordomar i igenkänningsresultat. Godkända värden är `none` som inaktiverar svordomar filtrering, `masked` som ersätter svordomar med asterisker `removed` som tar bort alla svordomar från resultatet, eller `tags` som lägger till ”svordomar”-taggar. Standardinställningen är `masked`. | Valfri |
| `PunctuationMode` | Anger hur du hanterar skiljetecken i igenkänningsresultat. Godkända värden är `none` som inaktiverar skiljetecken, `dictated` vilket medför att explicit skiljetecken `automatic` som gör att avkodaren handlar om skiljetecken, eller `dictatedandautomatic` vilket medför processens skiljetecken eller automatiskt. | Valfri |
 | `AddWordLevelTimestamps` | Anger om word på tidsstämplar ska läggas till utdata. Godkända värden är `true` vilket gör att word på tidsstämplar och `false` (standardvärdet) att inaktivera den. | Valfri |

### <a name="storage"></a>Storage

Batch stöder avskrift [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) för att läsa in ljud och avskrifter för skrivning till lagring.

## <a name="webhooks"></a>Webhooks 

Avsökning för transkription status kan inte de flesta prestanda eller ange den bästa användarupplevelsen. Om du vill söka efter status, kan du registrera återanrop som meddelar klienten när tidskrävande avskrift aktiviteterna har slutförts.

Mer information finns i [Webhooks](webhooks.md).

## <a name="sample-code"></a>Exempelkod

Hela exemplet finns i den [GitHub-exempellagringsplats](https://aka.ms/csspeech/samples) inuti den `samples/batch` underkatalog.

Du måste anpassa exempelkod med din prenumerationsinformation, regionen service, den SAS-URI som pekar på filen som transkribera och modellera ID: N om du vill använda en anpassad modell akustiska eller språk. 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Exempelkoden kommer Konfigurera klienten och begära avskrift. Den ska sedan söka efter information om status och Skriv ut information om förloppet avskrift.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Fullständig information om föregående anrop finns i vår [Swagger-dokument](https://westus.cris.ai/swagger/ui/index). För det fullständiga exemplet som visas här, gå till [GitHub](https://aka.ms/csspeech/samples) i den `samples/batch` underkatalog.

Anteckna asynkron konfigurationen för att skicka ljud och ta emot avskrift status. Klienten som du skapar är en .NET-HTTP-klient. Det finns en `PostTranscriptions` metod för att skicka ljud Filinformation och en `GetTranscriptions` metod för att ta emot resultaten. `PostTranscriptions` Returnerar en referens och `GetTranscriptions` används för att skapa en referens för att hämta status för avskrift.

Aktuella exempelkoden Ange inte en anpassad modell. Tjänsten använder baslinjemodeller för att skriva av den filen eller filerna. Om du vill ange modeller, kan du skicka på samma metod som modell-ID för akustiska och språkmodellen.

> [!NOTE]
> För baslinjen avskrifter behöver du inte deklarera ID för baslinjemodeller. Om du bara anger ett språk modell-ID (och inget akustisk modell-ID) markeras automatiskt en matchande akustisk modell. Om du bara anger en akustisk modell-ID, väljs automatiskt en matchande språkmodell.

## <a name="download-the-sample"></a>Hämta exemplet

Du hittar exemplet i den `samples/batch` katalogen i den [GitHub-exempellagringsplats](https://aka.ms/csspeech/samples).

> [!NOTE]
> Batch avskrift jobb är schemalagda efter bästa förmåga finns det inga Uppskattad tidsåtgång för när ett jobb kommer att ändras i körläge. En gång i körningstillstånd, bearbetas faktiska avskrift snabbare än ljud realtid.

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
