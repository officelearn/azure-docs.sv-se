---
title: Röst avskrifts tjänst
titleSuffix: Azure Cognitive Services
description: Konversations avskrift är en avancerad funktion i tal tjänsterna som kombinerar tal igenkänning i real tid, högtalar-ID och diarization. Konversations avskrift är perfekt för att skriva in person möten, med möjlighet att skilja på högtalarna, och det ger dig möjlighet att veta vem som är medveten om vad och när, så att deltagarna kan fokusera på mötet och snabbt följa upp nästa steg. Den här funktionen förbättrar också tillgängligheten. Med avskrift kan du aktivt engagera deltagare med hörsel nedskrivningar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562878"
---
# <a name="what-is-conversation-transcription"></a>Vad är en konversations avskrift?

Konversations avskrift är en avancerad funktion i tal tjänsterna som kombinerar tal igenkänning i real tid, högtalar-ID och diarization. Konversations avskrift är perfekt för att skriva in person möten, med möjlighet att skilja på högtalarna, och det ger dig möjlighet att veta vem som är medveten om vad och när, så att deltagarna kan fokusera på mötet och snabbt följa upp nästa steg. Den här funktionen förbättrar också tillgängligheten. Med avskrift kan du aktivt engagera deltagare med hörsel nedskrivningar.   

Konversations avskrift ger korrekt igenkänning med anpassningsbara tal modeller som du kan skräddarsy för att förstå bransch-och företagsspecifika vokabulär. Dessutom kan du para ihop konversations avskriften med tal enheter SDK för att optimera användningen av enheter med flera mikrofoner.

>[!NOTE]
> För närvarande rekommenderas konversations avskrift för små möten. Om du vill utöka konversations avskriften för stora möten i stor skala kan du kontakta oss.

Det här diagrammet visar maskin vara, program vara och tjänster som fungerar tillsammans med konversations avskrift.

![Diagrammet importera konversations avskrift](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> En cirkulär sju mikrofon mat ris med en speciell geometri konfiguration krävs. Information om specifikationer och design finns i [Microsoft Speech Device SDK Microphone](https://aka.ms/cts/microphone). Läs mer eller köp ett utvecklings paket i [Hämta Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Kom igång med konversations avskrift

Det finns tre steg som du måste vidta för att komma igång med konversations avskrift.

1. Samla in röst exempel från användare.
2. Skapa användar profiler med hjälp av användar röst exempel
3. Använd tal-SDK: n för att identifiera användare (talare) och skriva tal

## <a name="collect-user-voice-samples"></a>Samla in användar röst exempel

Det första steget är att samla in ljud inspelningar från varje användare. Användarens tal bör registreras i en tyst miljö utan bakgrunds ljud. Den rekommenderade längden för varje ljud sampling är mellan 30 sekunder och två minuter. Längre ljud exempel ger bättre precision vid identifiering av högtalare. Ljudet måste vara en mono-kanal med 16 KHz samplings frekvens.

Utöver den ovan nämnda vägledningen är hur ljud spelas in och lagras är upp till dig – en säker databas rekommenderas. I nästa avsnitt ska vi se hur det här ljudet används för att generera användar profiler som används med tal-SDK för att identifiera högtalare.

## <a name="generate-user-profiles"></a>Generera användar profiler

Därefter måste du skicka ljud inspelningarna som du har samlat in i tjänsten för att skapa signaturer för att verifiera ljudet och generera användar profiler. Tjänsten för att [skapa signaturer](https://aka.ms/cts/signaturegenservice) är en uppsättning REST API: er som gör att du kan generera och hämta användar profiler.

Om du vill skapa en användar profil måste du använda `GenerateVoiceSignature` API: et. Specifikations information och exempel kod är tillgängliga:

> [!NOTE]
> Konversations avskrift är för närvarande tillgängligt i "en-US" och "zh-cn" i följande regioner `centralus` : `eastasia`och.

* [REST-specifikation](https://aka.ms/cts/signaturegenservice)
* [Använda konversations avskrift](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Skriva och identifiera högtalare

I konversations avskrifter förväntas ljud strömmar och användar profiler i multikanal som indata för att generera avskrifter och identifiera högtalare. Ljud-och användar profil data skickas till konversations avskrifts tjänsten med hjälp av tal enheter SDK. Som tidigare nämnts krävs en cirkulär sju mikrofon mat ris och tal enheter SDK för att kunna använda konversations avskrifter.

>[!NOTE]
> Information om specifikationer och design finns i [Microsoft Speech Device SDK Microphone](https://aka.ms/cts/microphone). Läs mer eller köp ett utvecklings paket i [Hämta Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

Information om hur du använder konversations avskrift med tal enheter SDK finns i [så här använder du konversations](https://aka.ms/cts/howto)avskrifter.


## <a name="quick-start-with-a-sample-app"></a>Snabbstart med en exempel App

Microsoft Speech Device SDK har en snabb starts exempel app för alla enhets relaterade exempel. Konversations avskrift är en av dem. Du hittar den i [tal Device SDK Android snabb start](https://aka.ms/sdsdk-quickstart) med exempel App och dess käll kod för din referens.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om tal enheter SDK](speech-devices-sdk.md)
