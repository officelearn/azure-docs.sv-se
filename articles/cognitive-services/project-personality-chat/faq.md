---
title: Vanliga frågor och svar på person chatt - kognitiva Azure-tjänster | Microsoft Docs
description: Vanliga frågor och svar på person chatt
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.topic: faq
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 5304b25672f9951e004876dfd19af75ea7d86502
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355626"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Varför inte detta svara på alla frågor I fråga, precis som andra chatt robotar?

Projektet person chatt att förbättra bot med vanliga liten prata som innehåller personer och skapar en mer komplett användarupplevelse. Den har inte utformats för att driva långa konversationer om ämnen som inte är relevanta för den bot huvudfunktion. När den kan svara på alla konversationer begränsad – i betaversionen – till vanliga liten prata domäner.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Hur kan jag Anpassa person som passar min varumärken?

Välj den närmaste person tillgängliga standard personer. Idag, kan du ta redaktionell biblioteket och redigera svar så att de bättre passar ditt varumärke. I framtiden, kan du överföra utterances exempeldata från din valda person och hitta sin närmaste persona ID version. Det finns olika sätt att träna om och anpassa modellen.

## <a name="is-this-service-powering-existing-intelligent-agents-such-as-zo"></a>Är den här tjänsten startar befintliga intelligent agenter, till exempel Zo?

Tjänsterna startar Zo Cortana och projektet person chatta delar vissa liknande tekniker, men det finns olika grupper. Det har inbyggda learnings av erfarenheter med Zo och Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Den här tjänsten kan leda till felaktig kundupplevelser?

Om du vill ge en bättre upplevelse person chatta kan generera svar utöver de i redaktionell datamängden och försök att tolka indata från alla användare. Därför är det möjligt att ett svar inte verkar vara rätt i kontexten. Ett antal kontroller har försatts i för att förhindra att negativ svar som bygger på kunskap från intelligent agenter som Zo. Projektet person chatta är som standard att endast besvara identifierad användare avsikter. Du kanske vill testa om projektet person chatta lämpar sig för dina förhållanden. Din feedback är Välkommen om du ser något som kräver ytterligare utbildning. Om du använder den här tjänsten med dina kunder i framtiden, rekommenderar vi att du överväger anonymiserade loggning för att identifiera problem med levande användarinteraktioner.