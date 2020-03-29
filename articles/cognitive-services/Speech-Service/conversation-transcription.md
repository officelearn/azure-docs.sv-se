---
title: Konversationsavskrift (förhandsgranskning) - Taltjänst
titleSuffix: Azure Cognitive Services
description: Konversationsavskrift är en lösning för möten som kombinerar igenkänning, högtalar-ID och diarisering för att ge transkription av alla konversationer.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: ba56c7fb989658195f6394c7390c4f83027c7c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479747"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Vad är konversationsavskription i möten (förhandsgranskning)?

Konversationsavskrifter är en [tal-till-text-lösning](speech-to-text.md) som kombinerar taligenkänning, högtalaridentifiering och meningsattribution till varje högtalare (kallas även _diarisering)_ för att tillhandahålla realtids- och/eller asynkron transkription av alla konversationer. Konversationsavskrift skiljer högtalarna i en konversation för att avgöra vem som sa vad och när, och gör det enkelt för utvecklare att lägga till tal-till-text i sina program som utför diarisering med flera högtalare.

## <a name="key-features"></a>Huvudfunktioner

- **Tidsstämplar** - varje talare yttrande har en tidsstämpel, så att du enkelt kan hitta när en fras sades.
- **Läsbara transkriptioner** - transkriptioner har formatering och interpunktion läggs automatiskt till för att säkerställa att texten stämmer överens med vad som sades.
- **Användarprofiler** - användarprofiler genereras genom att samla in användarröstprover och skicka dem till signaturgenerering.
- **Högtalaridentifiering** - högtalare identifieras med hjälp av användarprofiler och en _högtalaridentifierare_ tilldelas var och en.
- **Flerhögtalardiarization** - avgöra vem som sa vad genom att syntetisera ljudströmmen med varje högtalaridentifierare.
- **Realtid transkription** - ge levande utskrifter av vem som säger vad och när medan samtalet sker.
- **asynkron transkription** – ge transkriptioner med högre noggrannhet med hjälp av en flerkanalig ljudström.

> [!NOTE]
> Även om konversationsavskrifter inte sätter någon gräns för antalet högtalare i rummet, är den optimerad för 2-10 högtalare per session.

## <a name="use-cases"></a>Användningsfall

### <a name="inclusive-meetings"></a>Inkluderande möten

För att göra möten inkluderande för alla, till exempel deltagare som är döva och hörselskadade, är det viktigt att ha transkription i realtid. Konversationsavskrift i realtidsläge tar mötesljud och avgör vem som säger vad, så att alla mötesdeltagare kan följa utskriften och delta i mötet utan dröjsmål.

### <a name="improved-efficiency"></a>Förbättrad effektivitet

Mötesdeltagarna kan fokusera på mötet och lämna anteckningar till konversationsavskrift. Deltagarna kan aktivt delta i mötet och snabbt följa upp nästa steg, med hjälp av utskriften istället för att göra anteckningar och eventuellt missa något under mötet.

## <a name="how-it-works"></a>Hur det fungerar

Detta är en översikt över hur konversationsavskription fungerar.

![Fönstret Import konversationsavskrifter](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Förväntade ingångar

- **Ljudström med flera kanaler** – Mer information om specifikation och design finns i [Microsoft Speech Device SDK Microphone](https://aka.ms/cts/microphone). Mer information om eller köpa ett utvecklingspaket finns i [Hämta Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).
- **Användarintexter** – Konversationsavskrift behöver användarprofiler före konversationen. Du måste samla in ljudinspelningar från varje användare och sedan skicka inspelningarna till [signaturgenereringstjänsten](https://aka.ms/cts/signaturegenservice) för att validera ljudet och generera användarprofiler.

## <a name="real-time-vs-asynchronous"></a>Realtid kontra asynkron

Konversation transkription erbjuder tre transkriptionslägen:

### <a name="real-time"></a>Realtid

Ljuddata bearbetas live för att returnera högtalaridentifierare + avskrift. Välj det här läget om kravet på transkriptionslösning är att ge konversationsdeltagarna en liveutskriftsvy av deras pågående konversation. Till exempel, bygga en ansökan om att göra möten mer tillgängliga döva och hörselskadade deltagare är ett idealiskt användningsfall för realtid transkription.

### <a name="asynchronous"></a>Asynkron

Ljuddata är batchbearbetning för att returnera högtalare identifierare och avskrift. Välj det här läget om kravet på transkriptionslösning är att ge högre noggrannhet utan live-utskriftsvy. Om du till exempel vill skapa ett program så att mötesdeltagarna enkelt kan komma ikapp missade möten använder du det asynkrona transkriptionsläget för att få transkriptionsresultat med hög noggrannhet.

### <a name="real-time-plus-asynchronous"></a>Realtid plus asynkron

Ljuddata bearbetas live för att returnera högtalaridentifierare + avskrift, och dessutom skapas en begäran för att också få en högkvalitativ avskrift genom asynkron bearbetning. Välj det här läget om ditt program har behov av transkription i realtid men också kräver en utskrift med högre noggrannhet för användning efter att konversationen eller mötet har ägt rum.

## <a name="language-support"></a>Stöd för språk

För närvarande stöder Konversation Transkription "en-US" och "zh-CN" i följande regioner: *centralus* och *eastasia*. Om du behöver ytterligare språksupport kontaktar du [Konversationsutskriftsfunktionen Crew](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Transkribera konversationer i realtid](how-to-use-conversation-transcription-service.md)
