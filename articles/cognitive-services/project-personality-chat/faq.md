---
title: Vanliga frågor och svar - personlighet chatt
titlesuffix: Azure Cognitive Services
description: Vanliga frågor och svar på personlighet chatt
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: personality-chat
ms.topic: faq
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 274b232f321b3ad739d18a8580b4a178dbde633e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219829"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Varför inte detta svara på alla frågor som jag ber om det, t.ex. andra chatt robotar?

Projektet personlighet chatt förbättra en bot med vanliga små talk som presenterar personlighet och skapar en bättre användarupplevelse. Det är inte utformat för att bedriva långa konversationer om ämnen som inte är relevanta för robotens huvudfunktion. De kan svara på alla konversationer, har begränsade i beta-versionen, till vanliga små tala domäner.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Hur kan jag Anpassa personlighet som passar min varumärke?

Välj den närmaste person standardegenskaper personer. Idag kan du ta språkliga biblioteket och redigera svar för att bättre passa ditt varumärke. I framtiden, kan du ladda upp exempeldata yttranden från din valda personlighet och hitta den närmaste person-ID versionen. Det finns också sätt att träna om och anpassa modellen.

## <a name="is-this-service-powering-existing-intelligent-agents-such-as-zo"></a>Den här tjänsten är är befintliga intelligent agenter, till exempel Zo?

Tjänsterna är Zo, Cortana och projekt personlighet chatta delar vissa liknande teknik, men det finns olika stackar. Det har tagit hänsyn erfarenheter från upplevelser med Zo och Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Den här tjänsten kan leda till dåliga kundupplevelser?

Om du vill ge en rikare upplevelse personlighet chatta kan generera svar utöver de i språkliga datauppsättningen och försök att tolka alla användarindata. Det är därför möjligt ett svar inte verkar rätt i kontexten. Ett antal kontroller har försatts i för att förhindra att svaren, bygger på kunskap från intelligent agenter som Zo. Projektet personlighet chatta är som standard svarar enbart på identifierad användare avsikter. Du kanske vill testa om projektet personlighet chatta lämpar sig för ditt fall. Din feedback är Välkommen om du ser något som behöver mer utbildningsmaterial. Om du använder den här tjänsten med kunderna i framtiden, rekommenderar vi du överväger anonym loggning för att identifiera problem med live användarinteraktioner.