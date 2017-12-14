---
title: "Azure Active Directory-hybrididentitet utformning - Bestäm hanteringsuppgifter för hybrid identity | Microsoft Docs"
description: "Med villkorlig åtkomstkontroll kontrollerar de särskilda villkor som du väljer när du autentiserar användaren och innan du tillåter åtkomst till programmet i Azure Active Directory. När dessa villkor är uppfyllda, autentiserade användaren och få tillgång till programmet."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 3257b5b9c714103773dfe646093cb632f500d459
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Planera för Hybrid Identity livscykel
Identiteten är hör till grunderna för enterprise mobility och programmet åtkomststrategi. Om du registrerar din mobila enhet eller SaaS-app, är din identitet avgörande för att få åtkomst till allt. På den högsta nivån omfattar en lösning för Identitetshantering enhetlig och synkroniseringen mellan din identitet databaser som innehåller automatisering och centralisera processen för etablering av resurser. Identitetslösning bör vara en centraliserad identitet i molnet och lokalt och även använda någon form av identitetsfederation att underhålla central autentisering på ett säkert sätt delar och samarbetar med externa användare och företag. Resurser mellan operativsystem och program till personer i eller kopplad till en organisation. Organisationsstruktur kan ändras för att anpassa provisioning principer och procedurer.

Det är också viktigt att du har en identitetslösning är för att underlätta för dina användare genom att ge dem med självbetjäning upplevelser för att hålla dem produktiva. Identitetslösning är mer robusta om det möjliggör enkel inloggning för användare över alla resurser som de behöver åtkomst till administratörer på alla nivåer anvisningarna standardiserade för att hantera autentiseringsuppgifter. Vissa nivåer för administration kan minskas eller elimineras beroende på etablering hanteringslösningen bredd. Dessutom kan du på ett säkert sätt distribuera administrationsfunktioner, manuellt eller automatiskt, mellan olika organisationer. Till exempel kan en domänadministratör fungera endast personer och resurser i domänen. Den här användaren kan utföra uppgifter för administration och etablering, men har inte behörighet att utföra konfigurationsåtgärder, till exempel skapa arbetsflöden.

## <a name="determine-hybrid-identity-management-tasks"></a>Fastställa hanteringsuppgifter för Hybrid Identity
Distribuera administrativa uppgifter i din organisation förbättrar är korrekta och effektiva administration och förbättrar balansen för arbetsbelastningen för en organisation. Följande är längskraften som definierar en robust identity management-systemet.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)

Om du vill definiera hybrid identity hanteringsuppgifter, måste du förstå vissa grundläggande egenskaper för den organisation som kommer att anta hybrididentitet. Det är viktigt att förstå de aktuella databaser som används för identitet källor. Måste de core-element har grundläggande krav och baserat på att du måste ställa mer detaljerade frågor som leder dig till ett bättre beslut för din identitetslösning.  

När du definierar dessa krav, se till att minst följande frågor besvaras

* Alternativ för etablering: 
  
  * Stöder hybrididentitetslösning en robust åtkomst kontohantering och provisioning system?
  * Hur är användare, grupper och lösenord som ska hanteras?
  * Svarar livscykelhantering identitet? 
    * Hur lång tid tar lösenord uppdateringar konto upphävande?
* Licenshantering: 
  
  * Stöder hybrid lösning hanterar licens Identitetshantering?
    * Om Ja, vilka funktioner är tillgängliga?
* Hanterar lösningen gruppbaserade licenshantering? 
  
      - Om Ja, är det möjligt att tilldela den en säkerhetsgrupp? 
       - Om Ja, kommer molnkatalogen automatiskt tilldela licenser till alla medlemmar i gruppen? 
        - Vad händer om en användare tas bort från gruppen eller senare läggs till kommer en licens automatiskt tilldelas eller tas bort efter behov? 
* Integrering med andra identitetsleverantörer för från tredje part:
* Den här kombinerade lösningen integreras med tredje parts identitetsleverantörer att implementera enkel inloggning?
* Är det möjligt att förena olika identitetsleverantörer till ett identitetssystem med enhetlig?
* Om Ja, hur och som är de och vilka funktioner som är tillgängliga?

## <a name="synchronization-management"></a>Hantering av datasynkronisering
Ett av målen för en identity manager för att kunna sätta alla identitetsleverantörer och hålla dem synkroniserade. Du behåller synkronisera data utifrån en auktoritativ master identitetsleverantör. I hybrididentitetsscenario med en modell med synkroniserade management, Hantera identiteter för alla användare och enhet i en lokal server och synkronisera de konton och eventuellt lösenord till molnet. Användaren anger den samma lösenord lokalt som han eller hon har i molnet och vid inloggning, lösenord har verifierats av identitetslösning. Den här modellen används en verktyget katalogsynkronisering.

![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Synkronisering av din hybrididentitetslösning till rätt design Kontrollera att följande frågor besvaras: • Vad är synkronisering lösningarna för hybrid identity-lösning?
• Vad är tillgängliga funktioner för enkel inloggning?
• Alternativ för identitetsfederation mellan B2B och B2C?

## <a name="next-steps"></a>Nästa steg
[Fastställa införandestrategin hybrid identity](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

