---
title: Om tal enheter SDK-Speech service
titleSuffix: Azure Cognitive Services
description: Kom igång med tal enheter SDK. Tal tjänsten fungerar med en mängd olika enheter och ljud källor. Nu kan du ta dina tal program till nästa nivå med matchad maskin vara och program vara. Tal enheter SDK är ett förjusterat bibliotek som är länkat till design paket för bygge, mikrofon mat ris.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 243072477c7d249d7066a7a448061c51a0bd2f34
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468731"
---
# <a name="about-the-speech-devices-sdk"></a>Om tal enheter SDK

[Tal tjänsterna](overview.md) fungerar med en mängd olika enheter och ljud källor. Nu kan du ta dina tal program till nästa nivå med matchad maskin vara och program vara. Tal enheter SDK är ett förjusterat bibliotek som är länkat till bygg satser med syftes utveckling, mikrofon mat ris.

Med hjälp av tal enheter SDK kan du:

* Testa snabbt nya röst scenarier.
* Integrera enkelt de molnbaserade tal tjänsterna i din enhet.
* Skapa en enastående användar upplevelse för dina kunder.

Tal enheternas SDK använder tal- [SDK](speech-sdk.md). Det använder tal-SDK för att skicka ljudet som bearbetas av vår algoritm för avancerad ljud bearbetning från enhetens mikrofon till tal-och [röst tjänsterna](overview.md). Det använder Multichannel-ljud för att ge bättre [tal igenkänning](speech-to-text.md) i långt fält via brus Undertryckning, avbrott, beamforming och dereverberation.

Du kan också använda tal enheter SDK för att bygga omgivande enheter som har ditt eget [anpassade nyckelord](speech-devices-sdk-create-kws.md) så att stacken som initierar en användar interaktion är unik för ditt varumärke.

Med tal enheter SDK kan du under lätta en mängd olika röst aktiverade scenarier, till exempel [röst assistenter](https://aka.ms/bots/speech/va), ordnings system, [konversations avskrift](conversation-transcription-service.md)och smarta högtalare. Du kan svara på användare med text, tala tillbaka till dem på en standard-eller [anpassad röst](how-to-customize-voice-font.md), tillhandahålla Sök resultat, [översätta](speech-translation.md) till andra språk och mycket annat. Vi ser fram emot att se vad du skapar!

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
