---
title: Åtkomstkontrollkrav för hybrididentitetsdesign Azure | Microsoft-dokument
description: Täcker identitetspelarna och identifierar åtkomstkrav för resurser för användare i en hybridmiljö.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60295151"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Fastställ åtkomstkontrollkrav för din hybrididentitetslösning
När en organisation utformar sin hybrididentitetslösning kan de också använda den här möjligheten för att granska åtkomstkraven för de resurser som de planerar att göra den tillgänglig för användare. Dataåtkomsten korsar alla fyra identitetspelare, som är:

* Administration
* Autentisering
* Auktorisering
* Granskning

De avsnitt som följer täcker autentisering och auktorisering i mer information, administration och granskning är en del av hybrididentitetslivscykeln. Läs [Ta reda på hybrididentitetshanteringsuppgifter](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) för mer information om dessa funktioner.

> [!NOTE]
> Läs [De fyra pelarna i identitet - Identitetshantering i Age of Hybrid IT](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) för mer information om var och en av dessa pelare.
> 
> 

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det finns olika scenarier för autentisering och auktorisering, dessa scenarier har specifika krav som måste uppfyllas av hybrididentitetslösningen som företaget ska anta. Scenarier som involverar kommunikation mellan företag (Företag till företag) kan lägga till en extra utmaning för IT-administratörer eftersom de måste se till att autentiserings- och auktoriseringsmetoden som används av organisationen kan kommunicera med sina affärspartners. Under designprocessen för autentiserings- och auktoriseringskrav ska du se till att följande frågor besvaras:

* Autentiserar och auktoriserar din organisation endast användare som finns i deras identitetshanteringssystem?
  * Finns det några planer för B2B scenarier?
  * Om ja, vet du redan vilka protokoll (SAML, OAuth, Kerberos eller Certifikat) som kommer att användas för att ansluta båda företagen?
* Stöder hybrididentitetslösningen som du ska anta dessa protokoll?

En annan viktig punkt att tänka på är var autentiseringsdatabasen som ska användas av användare och partner kommer att finnas och den administrativa modellen som ska användas. Tänk på följande två huvudalternativ:

* Centraliserad: I den här modellen kan användarens autentiseringsuppgifter, principer och administration centraliseras lokalt eller i molnet.
* Hybrid: I den här modellen kommer användarens autentiseringsuppgifter, principer och administration att centraliseras lokalt och replikeras i molnet.

Vilken modell din organisation ska anta varierar beroende på deras affärskrav, du vill svara på följande frågor för att identifiera var identitetshanteringssystemet ska finnas och det administrativa läget som ska användas:

* Har din organisation för närvarande en identitetshantering lokalt?
  * Om ja, planerar de att behålla det?
  * Finns det några reglerings- eller efterlevnadskrav som din organisation måste följa som avgör var identitetshanteringssystemet ska finnas?
* Använder din organisation enkel inloggning för appar som finns lokalt eller i molnet?
  * Om ja, påverkar antagandet av en hybrididentitetsmodell den här processen?

## <a name="access-control"></a>Åtkomstkontroll
Autentisering och auktorisering är centrala element för att möjliggöra åtkomst till företagsdata genom användarens validering, men det är också viktigt att kontrollera åtkomstnivån som dessa användare kommer att ha och nivån på åtkomstadministratörerna kommer att ha över resurserna som de hanterar. Din hybrididentitetslösning måste kunna ge detaljerad åtkomst till resurser, delegering och rollbasåtkomstkontroll. Se till att följande fråga besvaras om åtkomstkontroll:

* Har ditt företag mer än en användare med förhöjd behörighet att hantera ditt identitetssystem?
  * Om ja, behöver varje användare samma åtkomstnivå?
* Skulle ditt företag behöva delegera åtkomst till användare för att hantera specifika resurser?
  * Om ja, hur ofta detta händer?
* Skulle ditt företag behöva integrera åtkomstkontrollfunktioner mellan lokala resurser och molnresurser?
* Skulle ditt företag behöva begränsa tillgången till resurser enligt vissa villkor?
* Skulle ditt företag har något program som behöver anpassad kontroll tillgång till vissa resurser?
  * Om ja, var finns dessa appar (lokalt eller i molnet)?
  * Om ja, var finns dessa målresurser (lokalt eller i molnet)?

> [!NOTE]
> Skriv ner varje svar och försök förstå anledningen till svaret. [Definiera dataskyddsstrategi](plan-hybrid-identity-design-considerations-data-protection-strategy.md) kommer att gå igenom de tillgängliga alternativen och fördelar / nackdelar med varje alternativ.  Genom att svara på dessa frågor väljer du vilket alternativ som bäst passar dina affärsbehov.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa krav för incidenthantering](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

