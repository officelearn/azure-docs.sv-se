---
title: Hybrid identity design åtkomstkontrollkrav Azure | Microsoft Docs
description: Beskriver grundpelare för identitets- och identifiera åtkomstkraven för resurser för användare i en hybridmiljö.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: f71fc7547ef60cdc3d038705dbb04d396affdf0a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478237"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Fastställa krav på åtkomstkontroll för dina hybrididentitetslösning
När en organisation utforma sina hybrididentitetslösning, kan de också använda denna möjlighet för att granska åtkomstkraven för de resurser som de planerar att göra den tillgänglig för användare. Åtkomst till data mellan alla fyra grundläggande faktorer för identitet, som är:

* Administration
* Autentisering
* Auktorisering
* Granskning

Avsnitten som följer täcker autentisering och auktorisering i mer information, administration och granskning är en del av hybrid identitetslivscykel. Läs [fastställa hybrid identitetshanteringsaktiviteter](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) för mer information om dessa funktioner.

> [!NOTE]
> Läs [The fyra pelare med Identity - Identitetshantering i ålder Hybrid IT](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) för mer information om var och en av dessa pelare.
> 
> 

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det finns olika scenarier för autentisering och auktorisering, dessa scenarier kommer att ha specifika krav som måste uppfyllas av hybrididentitetslösning som företaget kommer att införa. Scenarier som omfattar Business to Business (B2B) kommunikation kan lägga till en extra utmaning för IT-administratörer eftersom de måste se till att den metod för autentisering och auktorisering som används av organisationen kan kommunicera med sina affärspartner. Se till att följande frågor besvaras under designprocessen för krav på autentisering och auktorisering:

* Kommer din organisation autentisera och auktorisera endast användare som finns i deras identitetshanteringssystem?
  * Finns det några planer för B2B-scenarier?
  * Om Ja, du redan vet vilka protokoll (SAML, OAuth, Kerberos eller certifikat) som används för att ansluta båda verksamheterna?
* Stöder hybrididentitetslösning som du kommer att införa stöd för dessa protokoll?

En annan viktig sak att tänka på är där lagringsplatsen autentisering som ska användas av användare och -partner kommer att finnas och administrationsmodellen som ska användas. Överväg följande två grundläggande alternativ:

* Centraliserad: i den här modellen användarens autentiseringsuppgifter, principer och administration kan vara lokalt centraliserad eller i molnet.
* Hybrid: i den här modellen användarens autentiseringsuppgifter, principer och administration är lokalt centraliserad och en replikerade i molnet.

Vilken modell som din organisation antar varierar beroende på sina affärsbehov, som du vill svara på följande frågor för att identifiera var identitetshanteringssystem kommer att finnas och administrativa läge ska använda:

* För närvarande har organisationen en Identitetshantering lokalt?
  * Om Ja, de planerar att hålla den?
  * Finns det några förordning eller efterlevnad krav som din organisation måste följa de avgör där hanteringssystem för identitet ska finnas?
* Använder organisationen enkel inloggning för appar som finns lokalt eller i molnet?
  * Om Ja, införandet av en modell för hybrid identity påverkar den här processen?

## <a name="access-control"></a>Åtkomstkontroll
Autentisering och auktorisering är kärnelement att aktivera åtkomst till företagets data via användarens verifiering, är det också viktigt att kontrollera åtkomstnivån som dessa användare kommer att ha och andelen åtkomst har resurser att de hanterar. Din hybrididentitetslösning måste kunna ge detaljerad åtkomst till resurser, delegering och rollbaserad åtkomstkontroll. Se till att följande frågan besvaras om åtkomstkontroll:

* Har företaget mer än en användare med förhöjda privilegier för att hantera ID-system?
  * Om Ja, varje användare behöver samma åtkomstnivå?
* Behöver företaget att delegera åtkomst till användare att hantera specifika resurser?
  * Om Ja, hur ofta detta sker?
* Behöver företaget att integrera funktioner för kontroll av åtkomst mellan lokala och molnbaserade resurser?
* Behöver företaget vill begränsa åtkomsten till resurser enligt vissa villkor?
* Behöver företaget alla program som behöver anpassad kontroll tillgång till vissa resurser?
  * Om Ja, var apparna finns (lokalt eller i molnet)?
  * Om Ja, där är de målresurser finns (lokalt eller i molnet)?

> [!NOTE]
> Se till att ner varje svar och försök förstå anledningen till svaret. [Definiera en strategi för Data Protection](plan-hybrid-identity-design-considerations-data-protection-strategy.md) tillgängliga alternativ och fördelar/nackdelar med varje alternativ.  Genom att besvara dessa frågor kan du välja vilket alternativ som bäst passar dina affärsbehov.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa kraven på incidentsvar](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

