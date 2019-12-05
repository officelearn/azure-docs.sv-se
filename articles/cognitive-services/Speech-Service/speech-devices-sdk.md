---
title: Om tal enheter SDK-Speech service
titleSuffix: Azure Cognitive Services
description: 'Kom igång med SDK: N för tal-enheter. Tal tjänsten fungerar med en mängd olika enheter och ljud källor. Tal Devices SDK är ett redan utformad bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 7f6793cab3d2d7cc54252bae3a7393f836256bdd
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815526"
---
# <a name="about-the-speech-devices-sdk"></a>Om Speech Devices SDK

[Tal tjänsten](overview.md) fungerar med en mängd olika enheter och ljud källor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är ett pretuned bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.

Med hjälp av tal Devices SDK kan du:

- Snabbt testa nya voice-scenarier.
- Integrera enkelt molnbaserad tal-tjänst i din enhet.
- Skapa en enastående användarupplevelse för kunderna.

SDK: N för tal enheter förbrukar den [tal SDK](speech-sdk.md). Den använder tal SDK för att skicka ljud som bearbetas av våra avancerade ljud bearbetning-algoritmen från enhetens mikrofon-matris på den [taltjänst](overview.md). Den använder flera ljud för att ge mer exakta långt fält [taligenkänning](speech-to-text.md) via bruset Undertryckning, eko, beamforming och dereverberation.

Du kan också använda tal enheter SDK för att bygga omgivande enheter som har ditt eget [anpassade nyckelord](speech-devices-sdk-create-kws.md) så att stacken som initierar en användar interaktion är unik för ditt varumärke.

Med tal enheter SDK kan du under lätta en mängd olika röst aktiverade scenarier, till exempel [röst assistenter](https://aka.ms/bots/speech/va), ordnings system, [konversations avskrift](conversation-transcription-service.md)och smarta högtalare. Du kan svara på användare med text, tala tillbaka till dem i en standard eller [anpassade röst](how-to-customize-voice-font.md), ange sökresultaten [översätta](speech-translation.md) till andra språk och mycket mer. Vi ser fram emot att se vad du skapa!

## <a name="get-the-speech-devices-sdk"></a>Hämta Speech Devices SDK

### <a name="android"></a>Android

För Android-enheter laddar du ned den senaste versionen av [Android Speech-enheterna SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

För Windows tillhandahålls exempel programmet som ett plattforms oberoende Java-program. Ladda ned den senaste versionen av [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Windows. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

### <a name="linux"></a>Linux

Exempel programmet tillhandahålls som ett plattforms oberoende Java-program för Linux. Ladda ned den senaste versionen av [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj din tal enhet](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
