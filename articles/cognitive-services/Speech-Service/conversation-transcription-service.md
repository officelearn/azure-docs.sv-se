---
title: Vad är en konversations avskrift (för hands version) – tal service
titleSuffix: Azure Cognitive Services
description: Konversations avskrift är en tal-till-text-lösning som kombinerar tal igenkänning, högtalar identifiering och menings tilldelningar till varje talare (kallas även diarization) för att tillhandahålla real tids och/eller asynkron avskrift av alla samtalet. Konversations avskrift gör konversationer inkluderade för alla, till exempel deltagare som är döva och har svårt att höra.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: f4b7591d5ddb138db0abbdb673cadc6afd71f315
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464726"
---
# <a name="what-is-conversation-transcription-preview"></a>Vad är en konversations avskrift (för hands version)?

Konversations avskrift är en [tal-till-text-](speech-to-text.md) lösning som kombinerar tal igenkänning, högtalar identifiering och menings tilldelningar till varje talare (kallas även _diarization_) för att tillhandahålla real tids och/eller asynkron avskrift av alla konversationer. Konversations avskrift skiljer sig från högtalare i en konversation för att fastställa vem som sade vad och när och gör det enkelt för utvecklare att lägga till tal till text till sina program som utför multi-talare-diarization.

## <a name="key-features"></a>Huvudfunktioner

- **Tidsstämplar** – varje talare uttryck har en tidsstämpel, så att du lätt kan hitta när en fras har sagts.
- **Läsbara** avskrifter – avskrifter har formatering och interpunktion läggs till automatiskt för att se till att texten stämmer överens med vad som sades.
- **Användar profiler** – användar profiler skapas genom insamling av användar röst exempel och skickas till skapande av signatur.
- **Högtalar identifiering** – högtalarna identifieras med hjälp av användar profiler och ett _högtalar-ID_ tilldelas till var och en.
- **Diarization för flera högtalare** – avgör vem som sade det genom att syntetisera ljud strömmen med varje högtalar-ID.
- **Real tids avskrift** – ange direkt avskrifter av vem som säger vad och när konversationen sker.
- **asynkron avskrift** – ge avskrifter med högre noggrannhet med hjälp av en ljud ström i Multichannel.

> [!NOTE]
> Även om konversations avskrift inte begränsar antalet högtalare i rummet, är det optimerat för 2-10-högtalare per session.

## <a name="use-cases"></a>Användningsfall

### <a name="inclusive-meetings"></a>Inkluderade möten

För att göra möten för alla, till exempel deltagare som är döva och hörselskadade, är det viktigt att du har avskrifter i real tid. Konversations avskrift i real tids läge tar Mötes ljud och avgör vem som säger vad, vilket gör det möjligt för alla Mötes deltagare att följa avskriften och delta i mötet utan fördröjning.

### <a name="improved-efficiency"></a>Förbättrad effektivitet

Mötes deltagare kan fokusera på mötet och lämna kommentarer till konversations avskrift. Deltagare kan aktivt delta i mötet och snabbt följa upp nästa steg, med hjälp av avskriften i stället för att anteckna och potentiellt sakna något under mötet.

## <a name="how-it-works"></a>Så här fungerar det

Det här är en översikt på hög nivå över hur konversations avskrift fungerar.

![Diagrammet importera konversations avskrift](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Förväntade indata

- **Ljud ström för flera kanaler** – information om specifikationer och design finns i [Microsoft Speech Device SDK-mikrofonen](https://aka.ms/cts/microphone). Läs mer eller köp ett utvecklings paket i [Hämta Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).
- **Användar röst exempel** – konversations avskrift kräver användar profiler i förväg i konversationen. Du måste samla in ljud inspelningar från varje användare och sedan skicka inspelningarna till [tjänsten Signature generation](https://aka.ms/cts/signaturegenservice) för att verifiera ljudet och generera användar profiler.

## <a name="real-time-vs-asynchronous"></a>Real tids vs. Asynchronous

Konversations avskriften har tre avskrifts lägen:

### <a name="real-time"></a>Real tids

Ljuddata bearbetas Live för att returnera högtalar-ID + avskrift. Välj det här läget om kravet på avskrifts lösning är att ge konversations deltagarna en Live-avskrifts visning av den pågående konversationen. Du kan till exempel skapa ett program för att göra möten mer lättillgängliga för döva och hörselskadade deltagare är ett idealiskt användnings fall för real tids avskrifter.

### <a name="asynchronous"></a>Asynkron

Ljuddata är en batch som bearbetas för att returnera högtalar identifierare och avskrift. Välj det här läget om kravet på avskrifts lösning är att ge högre noggrannhet utan Live-avskrifts läge. Om du till exempel vill skapa ett program som gör det möjligt för Mötes deltagare att enkelt komma igång med missade möten använder du det asynkrona avskrifts läget för att få resultat med hög precisions avskrifter.

### <a name="real-time-plus-asynchronous"></a>Real tids plus asynkront

Ljuddata bearbetas Live för att returnera högtalar-ID + avskrift, och dessutom skapas en begäran om att också få en hög noggrannhets avskrift genom asynkron bearbetning. Välj det här läget om ditt program har behov av avskrifter i real tid men kräver en högre noggrannhets avskrift för användning när konversationen eller mötet har inträffat.

## <a name="language-support"></a>Stöd för språk

För närvarande stöder konversations avskriften "en-US" och "zh-CN" i följande regioner: *Central* -och *asienöstra*. Om du behöver ytterligare stöd för nationella inställningar kontaktar du [konversationens Avskrifts funktion](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skriva över konversationer i real tid](how-to-use-conversation-transcription-service.md)
