---
title: Talenheter SDK - Taltjänst
titleSuffix: Azure Cognitive Services
description: Kom igång med SDK för talenheter. Taltjänsten fungerar med en mängd olika enheter och ljudkällor. Speech Devices SDK är ett förjusterat bibliotek som är parat med specialbyggda utvecklingspaket för mikrofonmatriser.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370652"
---
# <a name="what-is-the-speech-devices-sdk"></a>Vad är SDK för talenheter?

[Taltjänsten](overview.md) fungerar med en mängd olika enheter och ljudkällor. Nu kan du ta dina talprogram till nästa nivå med matchad maskinvara och programvara. Speech Devices SDK är ett förjusterat bibliotek som är parat med specialbyggda utvecklingspaket för mikrofonmatriser.

Speech Devices SDK kan hjälpa dig:

- Testa snabbt nya röstscenarier.
- Enklare att integrera den molnbaserade taltjänsten i enheten.
- Skapa en exceptionell användarupplevelse för dina kunder.

Talenheterna SDK förbrukar [Tal-SDK](speech-sdk.md). Använda våra avancerade ljudbehandlingsalgoritmer med enhetens mikrofonmatris för att skicka ljudet till [taltjänsten](overview.md). Det ger korrekt långt fält [taligenkänning](speech-to-text.md) via brusreducering, eko annullering, strålformning och dereverberation.

Du kan också använda SDK för talenheter för att skapa omgivande enheter som har ett eget [anpassat nyckelord](speech-devices-sdk-create-kws.md). Ett anpassat nyckelord ger en ledtråd som startar en användarinteraktion som är unik för ditt varumärke.

Speech Devices SDK möjliggör en mängd olika röstaktiverade scenarier, till exempel [röstassistenter,](https://aka.ms/bots/speech/va)drive-thru-beställningssystem, [konversationsavskription](conversation-transcription-service.md)och smarta högtalare. Du kan svara användare med text, säga tillbaka till dem med en standardröst eller [anpassad röst,](how-to-customize-voice-font.md)ge sökresultat, [översätta](speech-translation.md) till andra språk med mera. Vi ser fram emot att se vad du bygger!

## <a name="get-the-speech-devices-sdk"></a>Hämta Speech Devices SDK

### <a name="android"></a>Android

För Android hämtar enheter den senaste versionen av [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

För Windows tillhandahålls exempelprogrammet som ett Java-program över flera plattformar. Ladda ner den senaste versionen av [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet är byggt med Speech SDK-paketet och Eclipse Java IDE (v4) på 64-bitars Windows. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

### <a name="linux"></a>Linux

För Linux tillhandahålls exempelprogrammet som ett Java-program över flera plattformar. Ladda ner den senaste versionen av [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet är byggt med Speech SDK-paketet, och Eclipse Java IDE (v4) på 64-bitars Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

Ytterligare binärfiler tillhandahålls för att stödja kommande enheter, [Roobo v2 DDK,](https://aka.ms/sdsdk-download-roobov2) [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)och [GGEC Speaker](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj talenhet](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
