---
title: "Vanliga scenarier för Azure Data Catalog | Microsoft Docs"
description: "En översikt över vanliga scenarier för Azure Data Catalog, inklusive registrering och identifiering av värdefulla datakällor, aktivera affärsinformation med självbetjäning och samlar in befintliga kunskaper om datakällor och processer."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: maroche
ms.openlocfilehash: 8f333b96165144eb46e730a7a1dff52d064532bb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-common-scenarios"></a>Vanliga scenarier för Azure Data Catalog
Den här artikeln beskriver vanliga scenarier där Azure Data Catalog kan hjälpa din organisation som ger mer värde ur sina befintliga datakällor.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenario 1: Registrering av centrala datakällor
Organisationer har ofta många värdefulla datakällor. Dessa datakällor innehåller line-of-business-, online transaktionsbearbetning (OLTP) system, datalager och business intelligence/analytics-databaser. Antalet system och överlapp mellan dem, vanligtvis växer med tiden när verksamheten behöver utvecklas och själva verksamheten utvecklas via, till exempel sammanslagningar och förvärv.

Det kan vara svårt för organisationsmedlemmar i ska kunna hitta data i dessa datakällor. Följande frågor är alltför vanliga:

* Av de tre HR-system som används inom företaget, vilket ska jag använda att skapa den här typen av rapporten?
* Där ska gå för att hämta certifierade försäljningssiffrorna för räkenskapsåret som just avslutats?
* Som I fråga, eller vad handlar om att jag ska använda för att få åtkomst till data warehouse?
* Jag vet inte om dessa siffror är korrekta. Som kan jag be för att få information om hur dessa data ska användas innan jag dela den här instrumentpanelen med min grupp?

Azure Data Catalog kan ge svar på dessa och andra frågor. De centrala värdefulla, IT-hanterad datakällor som används mellan olika organisationer är ofta logiska startpunkten för ifyllning av katalogen. Även om alla användare kan registrera en datakälla, kan med katalogen kick-started med datakällor som är de mest sannolika värdet för största antal användare införa och användning av systemet. 

Komma igång med Azure Data Catalog, får identifiera och registrera viktiga datakällor som används av många olika team data användare din första steget.

Det här scenariot visar också en möjlighet att kommentera värdefulla datakällor så att de blir lättare att förstå och komma åt. En viktig del av denna åtgärd är att inkludera information om hur användarna kan begära åtkomst till datakällan. Du kan ange e-postadressen för den användare eller grupp som ansvarar för åtkomstkontroll för datakällan, länkar till befintliga verktyg eller dokumentationen eller fritext som beskriver hur åtkomstbegäran med Azure Data Catalog. Den här informationen hjälper medlemmar som identifiera registrerade datakällor men som inte ännu har behörighet att komma åt data så att du enkelt begära åtkomst med hjälp av de processer som definieras och styrs av datakällan ägare.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenario 2: Självbetjäning affärsinformation
Även om traditionella företagets business intelligence-lösningar fortsätter att vara en ovärderlig del av många organisationer data landskap kan har ändra ligger i linje med företag gjort självbetjäning BI fler och fler viktiga. Med hjälp av självbetjäning BI informationsarbetare och analytiker kan skapa sina egna rapporter, arbetsböcker och instrumentpaneler utan att behöva använda en central IT-avdelningen eller begränsas av för att IT-teamet schema och tillgänglighet.

I scenarier med självbetjäning BI kombinera användare ofta data från flera källor, som inte kanske har tidigare använts för BI och analys. Även om vissa av dessa datakällor kan redan vara kända, det kan vara en utmaning för att identifiera vad du gör för att leta upp och utvärdera potentiella datakällor för en viss uppgift.

Traditionellt den här identifieringen är en manuell: analytikerna använder sina peer-nätverksanslutningar för att identifiera andra som arbetar med data som begärs. När en datakälla finns och används upprepas den igen för varje efterföljande självbetjäning BI prestanda, med flera användare som utför en redundant manuella processen för identifiering.

Din organisation kan bryta cykeln av arbete med Azure Data Catalog. Efter identifiering av en datakälla traditionella sätt via en analytiker kan du registrera det så att den lättare kan identifieras av andra användare i framtiden. Analysen kan lägga till mer värde genom att kommentera registrerade datatillgångar, behöver den här anteckningen inte utföras samtidigt som registrering. Användare kan bidra när deras scheman bevilja gradvis mervärde till datakällor som har registrerats i katalogen.

Den här organiskt katalog innehållet är ett naturlig komplement till direkta registreringen av centrala datakällor. Före fylla katalogen med data som många användare måste vara en motivator för första användning och identifiering. Så att användarna kan registrera och kommentera ytterligare källor kan vara ett sätt att hålla dem och andra organisation ägnar åt.

Det är värt att nämna att det här scenariot fokuserar särskilt på självbetjäning BI, samma mönster och utmaningar gäller även för stora företag BI projekt som också. Din organisation kan förbättra alla arbete som inbegriper en manuell process för identifiering av datakälla med hjälp av Data Catalog.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenario 3: Samla in teamens specifika kunskaper
Hur vet du vilka data som du behöver utföra ditt jobb och var du hittar dessa data?

Om du har i dina jobb för en stund, vet förmodligen bara du. Du har gått igenom den gradvisa learning processen och över tid har lärt dig om de datakällor som är viktiga för din dagliga arbete.

När en ny medarbetare ansluter till ditt team, hur den personen känner vilka data som krävs för jobbet och var du hittar den?

Oddsen är den nya personen levereras till dig med dessa frågor.

Pågående överföringen av teamens specifika kunskaper är en del av datakällan identifieringsprocessen i organisationer stora och små. Högre och erfarna gruppmedlemmar har skapats knowledge åren och nyare gruppmedlemmar lärt be dem när de har frågor. Mest viktig information ofta finns bara i huvuden av några få nyckelpersoner och när de personerna är på semester eller lämnar teamet kan drabbas av organisationen.

Data experter göra normalt dokumentera sina kunskaper för att dela den via e-post eller i Word-dokument på en gruppwebbplats för SharePoint. Även om den här metoden kan vara bra, det inför en ny identifiering problem: hur personer vet vilken dokumentation finns och var den?

Din organisation har en enda central plats för att lagra och dela den här gruppens kunskaper och för att göra det enkelt kan identifieras med Azure Data Catalog. I Data Catalog data-experter kommenterar du datatillgångar direkt och innehåller länkar till befintliga dokumentationen. När organisationsmedlemmar använder katalogen för att identifiera en datakälla, hittar de inte bara källan själva, utan också kunskap som tidigare har funnits i hos din organisation experter.
