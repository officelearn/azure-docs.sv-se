---
title: Om tal enheter SDK-Speech service
titleSuffix: Azure Cognitive Services
description: 'Kom igång med SDK: N för tal-enheter. Speech-tjänsten fungerar med en mängd olika enheter och ljud datakällor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är ett redan utformad bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f2819dd2194193ffa8171034fdbe01ac4cb26174
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558916"
---
# <a name="about-the-speech-devices-sdk"></a>Om tal enheter SDK

[Tal tjänsterna](overview.md) fungerar med en mängd olika enheter och ljud källor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. Tal Devices SDK är ett pretuned bibliotek som är länkat till specialbyggda, mikrofon matris development Kit.

Med hjälp av tal Devices SDK kan du:

* Snabbt testa nya voice-scenarier.
* Integrera enkelt de molnbaserade tal tjänsterna i din enhet.
* Skapa en enastående användarupplevelse för kunderna.

SDK: N för tal enheter förbrukar den [tal SDK](speech-sdk.md). Det använder tal-SDK för att skicka ljudet som bearbetas av vår algoritm för avancerad ljud bearbetning från enhetens mikrofon till tal-och [röst tjänsterna](overview.md). Den använder flera ljud för att ge mer exakta långt fält [taligenkänning](speech-to-text.md) via bruset Undertryckning, eko, beamforming och dereverberation.

Du kan också använda programmet för tal enheter SDK för att bygga omgivande enheter som har ditt eget [anpassade aktiverings ord](speech-devices-sdk-create-kws.md) så att stacken som initierar en användar interaktion är unik för ditt varumärke.

Med tal enheter SDK kan du under lätta en mängd olika röst aktiverade scenarier, t. ex. [anpassade, första virtuella assistenter](https://aka.ms/bots/speech/va), enhets sorterings system, [konversations avskrift](conversation-transcription-service.md)och smarta högtalare. Du kan svara på användare med text, tala tillbaka till dem i en standard eller [anpassade röst](how-to-customize-voice-font.md), ange sökresultaten [översätta](speech-translation.md) till andra språk och mycket mer. Vi ser fram emot att se vad du skapa!

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
>
> [!div class="nextstepaction"]
> [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
