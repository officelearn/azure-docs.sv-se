---
title: Vad är konversationen deltagaren? -Kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Läs mer om konversationen deltagaren och hur det fungerar.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9cbf0e60382ef17d68aab47cf5f24ea9b8434f13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353991"
---
# <a name="what-is-conversation-learner"></a>Vad är konversationen deltagaren?

Konversationen deltagaren kan du skapa och utbilda vardagliga samtalsuttryck gränssnitt som lär sig från exemplet interaktioner. 

Till skillnad från traditionella metoder anser konversation deltagaren slutpunkt till slutpunkt-kontexten för en dialog att förbättra svar och designverktyget mer. Utsträckning en bred uppsättning uppgiftsorienterade användningsfall, konversation deltagaren gäller datorn lära dig att göra robotar och intelligent agenter som är mindre troligt att vara frustrerande för användare, medföra ytterligare customer servicekostnader i bakgrunden och intuitivt att interagera med.

Om du vill komma igång försätts utvecklaren prototypical dialogrutor som de vill imitera. När flera dialogrutor anges modellen uppdateras kontinuerligt och utvecklare kan se hur väl modellen generalisera. När modellen fungerar bra distribueras i bot till slutanvändare. Konversationen deltagaren loggar konversationer med användare och utvecklare kan granska dem. Om fel är spotted utvecklaren kan göra en korrigering på platsen och modellen är retrained och kan användas omedelbart.

Den här metoden minskar manuell kodning av dialog logik och möjliggör projektägare eller domän experter att bidra till ett gränssnitt för vardagliga samtalsuttryck utan föregående maskininlärning kunskap. Om den distribueras som en del av en bot eller smart enhet intelligent agent konversation deltagaren snabbt iterera nya kunskaper, beteenden eller kunskaper och snabbt förbättra kvaliteten. 

Konversationen deltagaren ger utvecklare möjlighet att öka hastigheten på marknaden och enheten lyckade dialoger över flera vardagliga samtalsuttryck kanaler via Microsoft Bot Framework eller fristående med sin egen infrastruktur.

Sammanfattning och viktiga funktioner:

- Konversationen deltagaren är AI först för att skapa uppgiftsorienterade robotar.

- Det bygger på en slutpunkt till slutpunkt återkommande neurala nätverk (LSTM) och lär sig direkt från flera Stäng exempel konversationer. 

- Aktiverar designers, utvecklare, företagsanvändare och call center anställda att bygga och underhålla robotar. 

- Ger möjlighet att uttrycka regler och gemensamma mening i kod.

- Under lära sessioner används modellen neurala nätverket för att poängsätta nästa uppsättning förväntade åtgärder i konversationen. Bot utvecklaren kan sedan välja rätt åtgärd och träna nätverket för att ge rätt svar.
 
- När utbildning är klar kan utvecklaren använda loggen dialogrutorna från användarinteraktioner som att göra korrigeringar bot svar och träna om modellen. 

- Anropa domänspecifika och tredjeparts-API: er för att slutföra uppgifter.

