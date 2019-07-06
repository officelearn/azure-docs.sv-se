---
title: Konversationen avskrift – Speech Services
titleSuffix: Azure Cognitive Services
description: Konversationen avskrift är en avancerad funktion av Taltjänster som kombinerar i realtid taligenkänning, talaridentifiering och diarization. Konversationen avskrift är perfekt för att skriva av personligen möten, med möjlighet att skilja talare, får du veta vem som SA vad och när, så att deltagare kan fokusera på mötet och snabbt som uppföljning om nästa steg. Den här funktionen förbättrar även tillgängligheten. Med transkription, kan du aktivt engagera deltagare med hörselnedsättning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 18de3e52d61afd8e1ef20f80c020c8b7d96b9757
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603256"
---
# <a name="what-is-conversation-transcription"></a>Vad är konversationen avskrift?

Konversationen avskrift är en avancerad funktion av Taltjänster som kombinerar i realtid taligenkänning, talaridentifiering och diarization. Konversationen avskrift är perfekt för att skriva av personligen möten, med möjlighet att skilja talare, får du veta vem som SA vad och när, så att deltagare kan fokusera på mötet och snabbt som uppföljning om nästa steg. Den här funktionen förbättrar även tillgängligheten. Med transkription, kan du aktivt engagera deltagare med hörselnedsättning.   

Konversationen avskrift levererar korrekt erkännande med anpassningsbara talmodeller som du kan anpassa för att förstå bransch och företagsspecifik ordförråd. Dessutom kan du koppla konversationen avskrift med SDK: N för tal-enheter att optimera upplevelsen för flera mikrofon enheter.

>[!NOTE]
> Konversationen avskrift rekommenderas för närvarande kan små möten. Om du vill utöka konversationen avskrift för stora möten i stor skala kan du kontakta oss.

Det här diagrammet illustrerar maskinvara, programvara och tjänster som fungerar tillsammans med konversationen avskrift.

![Importera konversationen avskrift Diagram](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> En cirkulär sju mikrofon-matris med specifika geometri konfiguration krävs. -Specifikationen och design information finns i [Microsoft Speech enheten SDK mikrofon](https://aka.ms/cts/microphone). Om du vill veta mer eller köpa ett development kit Se [hämta Microsoft Speech enhets-SDK](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Kom igång med konversationen avskrift

Det finns tre steg som du behöver utföra för att komma igång med konversationen avskrift.

1. Samla in röst exempel från användare.
2. Generera användarprofiler använda user voice-exempel
3. Använd tal-SDK för att identifiera användare (talare) och effektiv tal

## <a name="collect-user-voice-samples"></a>Samla in user voice-exempel

Det första steget är att samla in ljudinspelningar från varje användare. Användaren tal ska registreras i en tyst miljö utan bakgrundsljud. Det rekommenderade antalet för varje ljud exempel är mellan 30 sekunder och två minuter. Längre ljudsamplingar resulterar i bättre noggrannhet när du identifierar talare. Ljud måste vara mono kanal med en 16 KHz frekvens.

Så här ljud registreras och lagras är upp till dig – utöver de tidigare nämnda riktlinjer rekommenderas en säker databas. I nästa avsnitt ska vi gå igenom hur den här ljud används för att generera användarprofiler som används med Speech-SDK för att identifiera talare.

## <a name="generate-user-profiles"></a>Generera användarprofiler

Därefter måste du skicka ljudinspelningar du har samlat in till den Generation Signaturtjänsten för att validera ljudet och generera användarprofiler. Den [Generation Signaturtjänsten](https://aka.ms/cts/signaturegenservice) är en uppsättning REST API: er, som gör att du skapar och hämtar användarprofiler.

Om du vill skapa en användarprofil, måste du använda den `GenerateVoiceSignature` API. Specifikationen information och exempelkod är tillgängliga:

> [!NOTE]
> Konversationen avskrift är för närvarande tillgängligt i ”en-US” och ”zh-CN” i följande regioner: `centralus` och `eastasia`.

* [REST-specifikation](https://aka.ms/cts/signaturegenservice)
* [Hur du använder konversationen avskrift](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transkribera och identifiera talare

Konversationen avskrift förväntas ljudströmmar på flera kanaler och användarprofiler som indata för att generera avskrifter och identifiera talare. Ljud- och profildata skickas till konversationen avskrift-tjänsten med SDK: N för tal-enheter. Som tidigare nämnts måste en cirkulär sju mikrofon-matris och SDK för tal-enheter använda konversationen avskrift.

>[!NOTE]
> -Specifikationen och design information finns i [Microsoft Speech enheten SDK mikrofon](https://aka.ms/cts/microphone). Om du vill veta mer eller köpa ett development kit Se [hämta Microsoft Speech enhets-SDK](https://aka.ms/cts/getsdk).

Läs hur du använder konversationen avskrift med tal Devices SDK i [hur du använder konversationen avskrift](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Snabbstart med en exempelapp

Tal-enhets-SDK har en exempelapp för Snabbstart för alla enheter relaterade exempel. Konversationen avskrift är en av dem. Du hittar den i [tal enhets-SDK för android Snabbstart](https://aka.ms/sdsdk-quickstart) med exempelappen och källkoden för din referens.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om tal Devices SDK](speech-devices-sdk.md)
