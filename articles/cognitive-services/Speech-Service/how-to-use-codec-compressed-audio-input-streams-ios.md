---
title: Stream codec Compressed Audio med talet SDK på iOS
titleSuffix: Azure Cognitive Services
description: Lär dig hur du direktuppspelar komprimerat ljud till tal tjänsten med talet SDK på iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 2089f4191ddd57fa8dc19862bd195756c166f2d4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805866"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Gör så här: Använd codec-komprimerad ljud inspelning med talet SDK på iOS

Talet SDK: s **komprimerade ljud inspelnings** -API ger ett sätt att strömma komprimerat ljud till tal tjänsten med hjälp av en pull-eller push-dataström.

> [!IMPORTANT]
> Tal SDK-version 1.7.0 eller högre krävs för strömmande komprimerat ljud på iOS. Den stöds också för [ C++, C#och Java på Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) och [java i Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

För WAV/PCM ser du Mainline tal-dokumentationen. Utanför WAV/PCM stöds följande codec-komprimerade indataformat:

- MP3-FILEN
- OPUS/OGG
- FLAC
- ALAW i WAV-behållare
- MULAW i WAV-behållare

## <a name="prerequisites"></a>Krav

Hantering av komprimerat ljud implementeras med [gstreamer](https://gstreamer.freedesktop.org). Av licens skäl kan dessa funktioner inte levereras med SDK, men ett omslutnings bibliotek som innehåller dessa funktioner måste skapas av programutvecklare och levereras med apparna med hjälp av SDK.

För att bygga det här omslutnings biblioteket måste du först ladda ned och installera [GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Hämta sedan Xcode-projektet för [omslutnings biblioteket](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Öppna projektet i Xcode och skapa det för det **allmänna iOS-enhetens** mål – det fungerar inte för att skapa det för ett särskilt mål.

Build-steget genererar ett dynamiskt Framework-paket med ett dynamiskt bibliotek för alla nödvändiga arkitekturer med namnet på `GStreamerWrapper.framework`.

Det här ramverket måste inkluderas i alla appar som använder komprimerade ljud strömmar med Speech service SDK.

Använd följande inställningar i ditt Xcode-projekt för att göra detta:

1. Kopiera både `GStreamerWrapper.framework` som du precis har skapat och ramverket för Cognitive Services Speech SDK, som du kan ladda ned [härifrån, till](https://aka.ms/csspeech/iosbinary)katalogen som innehåller ditt exempel projekt.
1. Justera Sök vägarna till ramverken i _projekt inställningarna_.
   1. På fliken **Allmänt** under rubriken **inbäddade binärfiler** lägger du till SDK-biblioteket som ett ramverk: **lägg till inbäddade binärfiler** > **Lägg till andra...** > navigera till den katalog som du har valt och välj båda ramverken.
   1. Gå till fliken **Build Settings** (Versionsinställningar) och aktivera **All** (Alla).
1. Lägg till katalogen `$(SRCROOT)/..` i _Framework Search Paths_ (Sökvägar för ramverket) under rubriken **Search Paths** (Sökvägar).

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempel kod med codec komprimerad ljud inspelning

Om du vill strömma i ett komprimerat ljud format till tal tjänsten skapar du en `SPXPullAudioInputStream` eller `SPXPushAudioInputStream`.

Följande fragment visar hur du skapar en `SPXAudioConfiguration` från en instans av en `SPXPushAudioInputStream`, genom att ange MP3 som komprimerings format för data strömmen.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

Nästa kodfragment visar hur komprimerade ljud data kan läsas från en fil och pumpas till `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Se identifiera tal i Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
