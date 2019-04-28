---
title: Vanliga scenarier för Azure Data Catalog
description: En översikt över vanliga scenarier för Azure Data Catalog, inklusive registrering och upptäckt värdefulla datakällor, aktivera självbetjäningslösning för business intelligence och samla in befintliga kunskaper om datakällor och processer.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: e95cc64b9086a6fb4c5e2d42521a5fd3f44244ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003964"
---
# <a name="azure-data-catalog-common-scenarios"></a>Vanliga scenarier för Azure Data Catalog
Den här artikeln innehåller vanliga scenarier där Azure Data Catalog kan hjälpa organisationen att få ut mer av sina befintliga datakällor.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenario 1: Registrering av centrala datakällor
Organisationer har ofta många värdefulla datakällor. Dessa datakällor är bland annat line-of-business, online transaktionsbearbetning (OLTP) system, informationslager och business intelligence/analytics-databaser. Antalet system och överlapp mellan dem, ökar vanligtvis över tid enligt dina affärsbehov och själva verksamheten utvecklas via, till exempel sammanslagningar och förvärv av organisationer.

Det kan vara svårt för organisationsmedlemmar att veta var man ska hitta data i dessa datakällor. Följande frågor är alltför vanliga:

* Av de tre HR-system som används inom företaget, vilket ska jag använda att skapa den här typen av rapporten?
* Var ska gå för att få försäljningssiffrorna för certifierade för räkenskapsåret som precis har avslutats?
* Vem ska jag ställa eller hur ser processen ut jag ska använda för att få åtkomst till data warehouse?
* Jag vet inte om dessa siffror är rätt. Vem kan jag ställa om på hur dessa data ska användas innan jag dela den här instrumentpanelen med mitt team?

Azure Data Catalog kan ge svar på dessa och andra frågor. De centrala värdefulla, IT-hanterad datakällor som används mellan olika organisationer är ofta logiska startpunkt för att fylla i katalogen. Även om alla användare kan registrera en datakälla, kan med katalogen kick-started med datakällor som mest sannolikt att ange värdet till det största antalet användare införa och användning av systemet. 

Om du har kommit igång med Azure Data Catalog, kan identifiera och registrera viktiga datakällor som används av många olika team av datakonsumenterna vara din första steget.

Det här scenariot presenterar även möjlighet att kommentera värdefulla datakällor så att de blir lättare att förstå och komma åt. En viktig aspekt av det är att inkludera information om hur användarna kan begära åtkomst till datakällan. Med Azure Data Catalog kan ange du den e-postadressen för den användare eller grupp som ansvarar för åtkomstkontroll till datakällan, länkar till befintliga verktyg eller dokumentation eller fri text som beskriver hur du åtkomstbegäran. Den här informationen hjälper medlemmar som upptäcka registrerade datakällor, men som inte har behörighet att komma åt data så att du enkelt begära åtkomst med hjälp av de processer som definieras och styrs av datakällan ägare.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenario 2: Affärsinformation med självbetjäning
Även om traditionella business intelligence-lösningar som företagets fortsätter att vara en ovärderliga del av många organisationer datalandskap kan har ändra takten verksamhet gjort Självservice fler och fler viktiga. Med hjälp av BI av självbetjäningstyp kan informationsarbetare och analytiker skapa sina egna rapporter, arbetsböcker och instrumentpaneler utan att behöva en central IT-avdelningen eller begränsas av för att IT-teamet schema och tillgänglighet.

I BI självbetjäningsscenarier kombinera användare ofta data från flera källor, som inte kanske har tidigare använts för BI och analys. Även om vissa av dessa datakällor kan redan är känd, kan det vara svårt för att identifiera vad som ska göras för att hitta och utvärdera potentiella datakällor för en viss uppgift.

Traditionellt har den här identifieringen är en manuell: analytikerna använder sina peer-nätverksanslutningar för att identifiera andra som arbetar med data som som eftersöks. När en datakälla finns och används, upprepas den igen för varje efterföljande självbetjäning BI arbete, med flera användare som utför en redundant manuell process för identifiering.

Med Azure Data Catalog kan din organisation dela den här cykeln i arbete. Efter identifiering av en datakälla traditionella sätt, en analytiker kan du registrera det så att den blir enklare kan upptäckas av andra användare i framtiden. Analytiker kan lägga till mer värde av registrerade datatillgångar, behöver inte den här anteckningen ska kunna utföras samtidigt som registrering. Användare kan bidra med tiden då deras scheman tillståndet, gradvis mervärde till datakällor som registrerats i katalogen.

Den här organisk tillväxt av katalogen innehåll är en naturlig komplement till direkta registreringen av centrala datakällor. Före fylla katalogen med data som många användare måste vara en motivator för den första användningen och identifiering. Aktivera användare att registrera och kommentera ytterligare källor kan vara ett sätt att hålla dem och andra organisationsmedlemmar engagerade.

Det är värt att det här scenariot fokuserar särskilt på Självservice, samma mönster och utmaningar gäller även för storskaliga företagets BI projekt samt. Genom att använda Data Catalog kan kan din organisation förbättra alla arbete som inbegriper en manuell process för identifiering av datakällor.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenario 3: Samla in tidigare oskriven kunskap
Hur vet du vilka data som du behöver göra ditt jobb och var du hittar dessa data?

Om du har i jobbet ett tag, vet antagligen bara du. Du har gått igenom en gradvis inlärningsprocessen och med tiden har lärt dig om de datakällor som är nyckeln till ditt dagliga arbete.

När en ny medarbetare ansluter till ditt team, hur den personen känner vilka data som krävs för jobbet, och var du hittar det?

Oddsen är den nya personen som levereras till dig med dessa frågor.

Den här pågående överföring av tidigare oskriven kunskap är en del av datakällan identifieringsprocessen i stora och små. Senior och erfarna gruppmedlemmarna har skapats knowledge under åren och nyare gruppmedlemmarna har lärt dig att be dem när de har frågor. Ofta mest viktiga informationen finns bara i nytta av några få nyckelpersoner och när de personerna är på semester eller lämnar teamet kan drabbas av organisationen.

Dataexperter göra normalt dokumentera sina kunskaper för att dela den via e-post eller i Word-dokument på en SharePoint-gruppwebbplatsen. Även om den här metoden kan vara värdefulla, det inför en ny identifiering av problemet: hur människor vet vilka dokumentation finns och var du hittar det?

Med Azure Data Catalog kan har din organisation en gemensam, central plats för att lagra och dela den här tidigare oskriven kunskap och för att göra det enkelt att identifiera. I Data Catalog, dataexperter kommentera datatillgångar direkt och innehåller länkar till befintliga dokumentationen. När organisationsmedlemmar använder katalogen för att identifiera en datakälla kan hittar de inte bara datakällan själva, utan även den kunskap som tidigare fanns endast i din organisations experter hjärna.
