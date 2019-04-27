---
title: Vanliga frågor och svar - personlighet chatt
titlesuffix: Azure Cognitive Services
description: Vanliga frågor och svar på personlighet chatt
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 88dfbb2a547b44de1bb98ca536c7841570a12168
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829981"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Varför inte detta svara på alla frågor som jag ber om det, t.ex. andra chatt robotar?

Projektet personlighet chatt förbättra en bot med vanliga små talk som presenterar personlighet och skapar en bättre användarupplevelse. Det är inte utformat för att bedriva långa konversationer om ämnen som inte är relevanta för robotens huvudfunktion. De kan svara på alla konversationer, har begränsade i beta-versionen, till vanliga små tala domäner.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Hur kan jag Anpassa personlighet som passar min varumärke?

Välj den närmaste person standardegenskaper personer. Idag kan du ta språkliga biblioteket och redigera svar för att bättre passa ditt varumärke. I framtiden, kan du ladda upp exempeldata yttranden från din valda personlighet och hitta den närmaste person-ID versionen. Det finns också sätt att träna om och anpassa modellen.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>Den här tjänsten är är befintliga intelligent agenter, till exempel Zo?

Tjänsterna är Zo, Cortana och projekt personlighet chatta delar vissa liknande teknik, men det finns olika stackar. Det har tagit hänsyn erfarenheter från upplevelser med Zo och Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Den här tjänsten kan leda till dåliga kundupplevelser?

Om du vill ge en rikare upplevelse personlighet chatta kan generera svar utöver de i språkliga datauppsättningen och försök att tolka alla användarindata. Det är därför möjligt ett svar inte verkar rätt i kontexten. Det finns ett antal kontroller som ska hjälpa till att undvika oönskade svar, och de bygger på kunskap från intelligenta agenter som Zo. Som standard är Project Personality Chat inställt på att bara svara på användaravsikter som kan identifieras. Du kan testa om Project Personality Chat passar bra i just din situation. Om du ser någonting där det krävs mer inlärning får du gärna säga det till oss. Om du använder den här tjänsten med kunderna i framtiden, rekommenderar vi du överväger anonym loggning för att identifiera problem med live användarinteraktioner.
