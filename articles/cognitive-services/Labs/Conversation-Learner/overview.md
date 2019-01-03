---
title: Vad är Conversation Learner? -Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Läs mer om Konversationsdeltagare och hur det fungerar.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 0d2cca335206ee59b922a31ed3cf3ba24ef4ecd7
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790783"
---
# <a name="what-is-conversation-learner"></a>Vad är Conversation Learner?

Konversationsdeltagare kan du skapa och undervisa konversationsanpassade gränssnitt som lär sig från exemplet interaktioner. 

Till skillnad från traditionella metoder överväger Konversationsdeltagare slutpunkt till slutpunkt-kontexten för en dialogruta för att förbättra svar och leverera mer engagerande användarupplevelser. Med en bred uppsättning uppgiftsorienterade användningsfall Konversationsdeltagare gäller maskininlärning i bakgrunden och gör robotar och intelligent agenter som är mindre troligt att vara frustrerande för användare, medför ytterligare customer servicekostnader och mer spur mer intuitiv interaktioner.

Utvecklare börja genom att ange prototypical dialogrutorna för att efterlikna. Modellen lär sig när flera dialogrutor anges. När modellen fungerar bra, kan roboten distribueras till slutanvändare. Konversationsdeltagare loggar konversationer med användare och utvecklaren kan granska dem. Om misstag är spotted utvecklaren kan göra en korrigering på plats och modellen är modellkomponenten och kan användas direkt.

Den här metoden minskar manuell kodning av dialog logik och möjliggör företagsägare eller områdesexperter att bidra till ett konversationsanpassade gränssnitt utan tidigare kunskaper för maskininlärning. Om den distribueras som en del av en bot, smarta enheter eller intelligent agent Konversationsdeltagare snabbt kan iterera nya kunskaper eller beteenden kompetenser och snabbt förbättra deras kvalitet. 

Konversationsdeltagare gör det möjligt för utvecklare att öka snabbt till marknaden och drive lyckad dialoger i flera konversationsanpassade kanaler via Microsoft Bot Framework eller separat med sin egen infrastruktur.

Sammanfattning och viktiga funktioner:

- Konversationsdeltagare är ett AI-första sätt att skapa åtgärds-inriktade robotar.

- Den bygger på en slutpunkt till slutpunkt återkommande neurala nätverk (LSTM) och lär sig direkt från flera aktivera exempel på konversationer. 

- Möjliggör designers, utvecklare, företagsanvändare och anrop center arbeten att bygga och underhålla robotar. 

- Ger möjlighet att uttrycka affärsregler och sunt förnuft i kod.

- Under undervisa sessioner, används neuralt nätverk modellen för att bedöma nästa uppsättning förväntade åtgärder i konversationen. Bot utvecklaren kan sedan välja rätt åtgärd och träna nätverket för att ge rätt svar.
 
- När utbildning har slutförts kan utvecklaren använda log dialogrutorna från användarinteraktioner att göra korrigeringar bot svar och träna om modellen. 

- Anropa domänspecifika och tredjeparts-API: er för att slutföra uppgifter.

