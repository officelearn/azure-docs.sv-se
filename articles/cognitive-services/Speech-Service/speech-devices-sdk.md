---
title: Om enheterna som tal SDK – Speech Services
titleSuffix: Azure Cognitive Services
description: 'Kom igång med SDK: N för tal-enheter. Speech Services fungerar med en mängd olika enheter och ljud datakällor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är ett redan utformad bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 341f35d04c7f3f591198acbbf05ac32afe84e95c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718540"
---
# <a name="about-the-speech-devices-sdk"></a>Om enheterna som tal SDK

Den [Taltjänster](overview.md) fungerar med en mängd olika enheter och ljud datakällor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är ett pretuned bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.

Med hjälp av tal Devices SDK kan du:

* Snabbt testa nya voice-scenarier.
* Integrera enkelt molnbaserade Speech Services i din enhet.
* Skapa en enastående användarupplevelse för kunderna.

SDK: N för tal enheter förbrukar den [tal SDK](speech-sdk.md). Den använder tal SDK för att skicka ljud som bearbetas av våra avancerade ljud bearbetning-algoritmen från enhetens mikrofon-matris på den [Taltjänster](overview.md). Den använder flera ljud för att ge mer exakta långt fält [taligenkänning](speech-to-text.md) via bruset Undertryckning, eko, beamforming och dereverberation.

Du kan också använda tal Devices SDK för att skapa omgivande enheter som har en egen [anpassade wake word](speech-devices-sdk-create-kws.md) så Låt dig ledas som initierar någon interaktion från användaren är unikt för ditt varumärke.

Tal Devices SDK som underlättar en mängd olika scenarier för röst-aktiverade [anpassad röst första virtuella assistenter](https://aka.ms/bots/speech/va), enhet – till och ordning system, [konversationen avskrift](conversation-transcription-service.md), och smart talare. Du kan svara på användare med text, tala tillbaka till dem i en standard eller [anpassade röst](how-to-customize-voice-font.md), ange sökresultaten [översätta](speech-translation.md) till andra språk och mycket mer. Vi ser fram emot att se vad du skapa!

## <a name="get-the-speech-devices-sdk"></a>Hämta Speech Devices SDK

### <a name="android"></a>Android

För Android-enheter hämta den senaste versionen av den [Android tal enheter SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Exempelprogrammet har angetts som ett plattformsoberoende Java-program för Windows. Ladda ned den senaste versionen av den [JRE tal Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet har byggts med tal SDK-paketet och Eclipse Java IDE (v4) på 64-bitars Windows. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

### <a name="linux"></a>Linux

Exempelprogrammet har angetts som ett plattformsoberoende Java-program för Linux. Ladda ned den senaste versionen av den [JRE tal Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet har byggts med tal SDK-paketet och Eclipse Java IDE (v4) på 64-bitars Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9). Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj enheten tal](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Skaffa en prenumerationsnyckel för Speech Services utan kostnad](get-started.md)
