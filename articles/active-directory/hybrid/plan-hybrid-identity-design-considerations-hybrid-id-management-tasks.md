---
title: Hybrididentitetsdesign – hanteringsuppgifter Azure | Microsoft-dokument
description: Med kontrollen Villkorlig åtkomst kontrollerar Azure Active Directory de specifika villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet. När dessa villkor är uppfyllda autentiseras användaren och får åtkomst till programmet.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a829d39ff21a1abeafd3b4362747894d196d9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109388"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Planera för hybrididentitetslivscykeln
Identitet är en av grunderna för din strategi för företagsmobilitet och tillgång till program. Oavsett om du loggar in på din mobila enhet eller SaaS-app är din identitet nyckeln till att få tillgång till allt. På högsta nivå omfattar en identitetshanteringslösning enande och synkronisering mellan dina identitetsregister, vilket inkluderar automatisering och centralisering av processen för att etablera resurser. Identitetslösningen ska vara en centraliserad identitet i lokala och moln och även använda någon form av identitetsfederation för att upprätthålla centraliserad autentisering och säkert dela och samarbeta med externa användare och företag. Resurserna sträcker sig från operativsystem och program till personer i eller anslutna till en organisation. Organisationsstrukturen kan ändras för att tillgodose etableringsprinciper och procedurer.

Det är också viktigt att ha en identitetslösning som är inriktad på att ge användarna möjlighet genom att ge dem självbetjäningsupplevelser för att hålla dem produktiva. Din identitetslösning är mer robust om den aktiverar enkel inloggning för användare på alla resurser de behöver åtkomst. Administratörer på alla nivåer kan använda standardiserade procedurer för att hantera användarautentiseringsuppgifter. Vissa administrationsnivåer kan minskas eller elimineras, beroende på bredden av etableringshanteringslösningen. Dessutom kan du på ett säkert sätt distribuera administrationsfunktioner, manuellt eller automatiskt, mellan olika organisationer. En domänadministratör kan till exempel bara betjäna personer och resurser i den domänen. Den här användaren kan utföra administrativa uppgifter och etableringsuppgifter, men har inte behörighet att utföra konfigurationsuppgifter, till exempel skapa arbetsflöden.

## <a name="determine-hybrid-identity-management-tasks"></a>Ta reda på hybrididentitetshanteringsuppgifter
Om du distribuerar administrativa uppgifter i organisationen förbättras administrationens noggrannhet och effektivitet och balansen i arbetsbelastningen för en organisation förbättras. Följande är de pivoter som definierar ett robust identitetshanteringssystem.

 ![identitetshantering överväganden](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Om du vill definiera hybrididentitetshanteringsuppgifter måste du förstå några grundläggande egenskaper hos organisationen som ska anta hybrididentitet. Det är viktigt att förstå de aktuella databaserna som används för identitetskällor. Genom att känna till dessa kärnelement, kommer du att ha grundläggande krav och baserat på att du kommer att behöva ställa mer detaljerade frågor som leder dig till en bättre design beslut för din identitet lösning.  

När du definierar dessa krav, se till att åtminstone följande frågor besvaras

* Etableringsalternativ: 
  
  * Stöder hybrididentitetslösningen ett robust system för hantering och etablering av kontoåtkomst?
  * Hur hanteras användare, grupper och lösenord?
  * Är identitetslivscykelhanteringen responsiv? 
    * Hur lång tid tar det att avbryta kontots kontoavstängning?
* Licenshantering: 
  
  * Hanterar hybrididentitetslösningen licenshantering?
    * Om ja, vilka funktioner finns tillgängliga?
  * Hanterar lösningen gruppbaserad licenshantering? 
  
    * Om ja, är det möjligt att tilldela en säkerhetsgrupp till den? 
    * Om ja, kommer molnkatalogen automatiskt tilldela licenser till alla medlemmar i gruppen? 
    * Vad händer om en användare senare läggs till, eller tas bort från gruppen, tilldelas eller tas bort automatiskt? 
* Integrering med andra tredjepartsidentitetsleverantörer:
  * Kan den här hybridlösningen integreras med identitetsleverantörer från tredje part för att implementera enkel inloggning?
  * Är det möjligt att förena alla olika identitetsleverantörer i ett sammanhållet identitetssystem?
  * Om ja, hur och vilka är de och vilka funktioner finns tillgängliga?

## <a name="synchronization-management"></a>Hantering av synkronisering
Ett av målen för en identitetshanterare, att kunna ta med alla identitetsleverantörer och hålla dem synkroniserade. Du behåller data synkroniserade baserat på en auktoritär huvudidentitetsprovider. I ett hybrididentitetsscenario, med en synkroniserad hanteringsmodell, hanterar du alla användar- och enhetsidentiteter i en lokal server och synkroniserar kontona och eventuellt lösenord till molnet. Användaren anger samma lösenord lokalt som de gör i molnet, och vid inloggning verifieras lösenordet av identitetslösningen. Den här modellen använder ett katalogsynkroniseringsverktyg.

![katalogsynkronisering](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) För att korrekt utforma synkroniseringen av hybrididentitetslösningen, se till att följande frågor besvaras:
*    Vilka är synkroniseringslösningarna tillgängliga för hybrididentitetslösningen?
*    Vilka är de enda inloggningsfunktionerna tillgängliga?
*    Vilka är alternativen för identitetsfederation mellan B2B och B2C?

## <a name="next-steps"></a>Nästa steg
[Ta reda på strategi för antagande av hybrididentitetshantering](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

