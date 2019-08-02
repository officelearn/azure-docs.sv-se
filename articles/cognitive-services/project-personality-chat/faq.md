---
title: Vanliga frågor och svar – Personality Chat
titlesuffix: Azure Cognitive Services
description: Vanliga frågor och svar om Personality Chat
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: e763e3c3731858e20226efbd354927f38c3d5d70
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704224"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Varför kan jag inte svara på det här svaret, t. ex. andra chatt-robotar?

Med projektets personlighet Chat kan du förbättra en robot med vanlig mindre information som demonstrerar personlighet och skapar en mer fullständig användar upplevelse. Den har inte utformats för långa konversationer om ämnen som inte är relevanta för robotens primära funktion. Även om det kan svara på alla konversationer, är den begränsad, i Beta versionen, till vanliga små sittande domäner.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>Hur kan jag anpassa sin personlighet att passa mitt varumärke?

Välj den närmast personen från den tillgängliga standard personer. I dag kan du ta redaktionellt bibliotek och redigera svaren så att de passar ditt varumärke bättre. I framtiden kan du ladda upp en exempel uppsättning yttranden från din valda personlighet och hitta dess närmaste person-ID-version. Det finns också olika sätt att omträna och anpassa modellen.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>Driver den här tjänsten befintliga intelligenta agenter som zo?

Tjänsterna Powering Zo, Cortana och Project Personality Chat dela liknande metoder, men är olika stackar. Den har ingående information från upplevelserna med zo och Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Kan den här tjänsten leda till dåliga kund upplevelser?

För att ge en bättre upplevelse kan Personality Chat generera svar utöver dem i klipp data uppsättningen och försöker tolka alla användarindata. Därför är det möjligt att ett svar inte verkar korrekt i kontexten. Det finns ett antal kontroller som ska hjälpa till att undvika oönskade svar, och de bygger på kunskap från intelligenta agenter som Zo. Som standard är Project Personality Chat inställt på att bara svara på användaravsikter som kan identifieras. Du kan testa om Project Personality Chat passar bra i just din situation. Om du ser någonting där det krävs mer inlärning får du gärna säga det till oss. Om du använder den här tjänsten med dina kunder i framtiden rekommenderar vi att du betraktar anonymiserats-loggning för att hjälpa dig att identifiera problem med användar interaktioner i real tid.
