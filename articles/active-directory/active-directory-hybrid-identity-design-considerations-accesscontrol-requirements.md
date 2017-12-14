---
title: "Azure Active Directory hybrid identity designöverväganden - fastställa krav på åtkomstkontroll | Microsoft Docs"
description: "Beskriver pelare för identitets- och identifiera krav för resurser för användare i en hybridmiljö."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 069c83a0bbbb21ba85aea07375c186fe0b11c781
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Fastställa krav på åtkomstkontroll för din hybrididentitetslösning
När du designar en organisation sina hybrididentitetslösning kan de också använda passa på att granska åtkomstkraven för de resurser som de planerar att göra den tillgänglig för användare. Åtkomst till data mellan alla fyra pelare identitet, som är:

* Administration
* Autentisering
* Auktorisering
* Granskning

Avsnitten som följer beskriver autentisering och auktorisering mer detaljerat, administration och granskning är en del av hybrid identity livscykel. Läs [fastställa hanteringsuppgifter för hybrid identity](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) mer information om dessa funktioner.

> [!NOTE]
> Läs [i fyra pelare av Identity - Identity Management ålder Hybrid den](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) mer information om var och en av dessa pelare.
> 
> 

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det finns olika scenarier för autentisering och auktorisering, dessa scenarier har specifika krav som måste uppfyllas av hybrididentitetslösning som företaget kommer att anta. Scenarier som involverar Business to Business (B2B) kommunikation kan lägga till en extra utmaning för IT-administratörer eftersom de måste se till att autentisering och auktorisering metoden som används i organisationen kan kommunicera med sina affärspartner. När du utforma krav på autentisering och auktorisering, kontrollerar du att följande frågor besvaras:

* Kommer din organisation autentisera och auktorisera endast användare som finns i deras identitet hanteringssystemet?
  * Finns det några planer för B2B-scenarier?
  * Om Ja, du redan vet vilka protokoll (SAML OAuth, Kerberos, token eller certifikat) används för att ansluta båda företag?
* Stöder hybrididentitetslösning som du kommer att anta stöd för dessa protokoll?

En annan viktig sak att tänka på är där databasen för autentisering som ska användas av användare och partners kommer att finnas och administrationsmodellen som ska användas. Överväg följande två grundläggande alternativ:

* Centraliserad: i den här modellen användarens autentiseringsuppgifter, principer och administration kan vara lokalt centraliserad eller i molnet.
* Hybrid: i den här modellen användarens autentiseringsuppgifter, principer och administration blir lokalt centraliserad och en replikerade i molnet.

Vilken modell som företaget antar varierar beroende på deras affärskrav som du vill besvara följande frågor för att identifiera där hanteringssystemet identitet kommer att finnas och administrativa läge ska använda:

* För närvarande har organisationen en Identitetshantering lokalt?
  * Om Ja, kommer de att den?
  * Finns det några förordning eller efterlevnad krav som din organisation måste följa de bestämmer där hanteringssystemet identitet ska finnas?
* Använder organisationen enkel inloggning för appar som finns lokalt eller i molnet?
  * Om Ja, införandet av en modell för hybrid identity påverkar processen?

## <a name="access-control"></a>Access Control
Autentisering och auktorisering är core element att aktivera åtkomst till företagets data via användarens verifiering, men det är också viktigt att styra åtkomstnivån som dessa användare har och hur administratörer har över de resurser som de hanterar är. Din hybrididentitetslösning måste kunna tillhandahålla detaljerade åtkomst till resurser, delegering och rollbaserad åtkomstkontroll. Se till att följande fråga besvaras om åtkomstkontroll:

* Har företaget flera användare med förhöjda privilegier för att hantera identitet systemet?
  * Om Ja, varje användare behöver samma åtkomstnivå?
* Behöver företaget att delegera åtkomst till användare att hantera specifika resurser?
  * Om Ja, hur ofta detta sker?
* Behöver företaget att integrera kontrollen åtkomstfunktioner mellan lokala och moln resurser?
* Behöver företaget att begränsa åtkomsten till resurser enligt vissa villkor?
* Företaget skulle ha alla program som behöver en anpassad kontroll tillgång till vissa resurser?
  * Om Ja, var apparna finns (lokalt eller i molnet)?
  * Om Ja, där är de mål resurser (lokalt eller i molnet)?

> [!NOTE]
> Se till att varje svar och försök förstå anledningen till svaret. [Definiera en strategi för Data Protection](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) kommer att överskrida det tillgängliga alternativ och fördelar/nackdelar med varje alternativ.  Genom att besvara frågorna väljer du vilket alternativ som bäst passar dina affärsbehov.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Fastställa kraven på incidentsvar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

