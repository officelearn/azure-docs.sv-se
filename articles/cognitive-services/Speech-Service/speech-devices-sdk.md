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
ms.date: 02/12/2020
ms.author: erhopf
ms.openlocfilehash: 40179c7d421a26fd9a77b435668f75ec6fde8ab1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136981"
---
# <a name="about-the-speech-devices-sdk"></a>Om Speech Devices SDK

[Tal tjänsten](overview.md) fungerar med en mängd olika enheter och ljud källor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är ett pretuned bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.

Med hjälp av tal Devices SDK kan du:

- Snabbt testa nya voice-scenarier.
- Integrera enkelt molnbaserad tal-tjänst i din enhet.
- Skapa en enastående användarupplevelse för kunderna.

Tal enheternas SDK använder tal- [SDK](speech-sdk.md). Använd våra avancerade algoritmer för ljud bearbetning med enhetens mikrofon mat ris för att skicka ljudet till [tal tjänsten](overview.md). Det ger korrekt [tal igenkänning](speech-to-text.md) i långt fält via brus Undertryckning, avvisning av eko, beamforming och dereverberation.

Du kan också använda tal enhets-SDK: n för att bygga omgivande enheter som har ditt eget [anpassade nyckelord](speech-devices-sdk-create-kws.md). Ett anpassat nyckelord är en stack-ikon som startar en användar interaktion som är unik för ditt varumärke.

Med tal enheter SDK kan du använda olika röst funktioner, till exempel [röst assistenter](https://aka.ms/bots/speech/va), enhets sorterings system, [konversations avskrift](conversation-transcription-service.md)och smarta högtalare. Du kan svara på användare med text, tala tillbaka till dem på en standard-eller [anpassad röst](how-to-customize-voice-font.md), tillhandahålla Sök resultat, [översätta](speech-translation.md) till andra språk och mycket annat. Vi ser fram emot att se vad du skapa!

## <a name="get-the-speech-devices-sdk"></a>Hämta Speech Devices SDK

### <a name="android"></a>Android

För Android laddar enheter ned den senaste versionen av [Android Speech-enheterna SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

För Windows tillhandahålls exempel programmet som ett plattforms oberoende Java-program. Ladda ned den senaste versionen av [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Windows. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

### <a name="linux"></a>Linux

För Linux tillhandahålls exempel programmet som ett plattforms oberoende Java-program. Ladda ned den senaste versionen av [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

Det finns ytterligare binärfiler som stöder kommande enheter, [Roobo v2-DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)och [GGEC-högtalare](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj din tal enhet](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
