---
title: Identitetsskydd och B2B-användare - Azure Active Directory
description: Använda Identitetsskydd med B2B-användare hur det fungerar och kända begränsningar
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881313"
---
# <a name="identity-protection-and-b2b-users"></a>Identitetsskydd och B2B-användare

Med Azure AD B2B-samarbete kan organisationer tillämpa riskbaserade principer för B2B-användare med identity protection. Dessa principer konfigureras på två sätt:

- Administratörer kan konfigurera de inbyggda riskbaserade principer för identitetsskydd som gäller för alla appar som innehåller gästanvändare.
- Administratörer kan konfigurera sina principer för villkorlig åtkomst med hjälp av inloggningsrisk som ett villkor som inkluderar gästanvändare.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>Hur utvärderas risk för B2B-samarbetsanvändare

Användarrisken för B2B-samarbetsanvändare utvärderas i deras arbetskatalog. Inloggningsrisken i realtid för dessa användare utvärderas i resurskatalogen när de försöker komma åt resursen.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>Begränsningar av identitetsskydd för B2B-samarbetsanvändare

Det finns begränsningar i implementeringen av Identity Protection för B2B-samarbetsanvändare i en resurskatalog på grund av deras identitet som finns i deras arbetskatalog. De viktigaste begränsningarna är följande:

- Om en gästanvändare utlöser användarriskprincipen för identitetsskydd för att tvinga fram återställning av lösenord **blockeras de**. Det här blocket beror på oförmågan att återställa lösenord i resurskatalogen.
- **Gästanvändare visas inte i rapporten riskfyllda användare**. Denna förlust av synlighet beror på den riskbedömning som sker i B2B-användarens arbetskatalog.
- Administratörer **kan inte avvisa eller åtgärda en riskfylld B2B-samarbetsanvändare** i sin resurskatalog. Den här förlusten av funktioner beror på att administratörer i resurskatalogen inte har åtkomst till B2B-användarens arbetskatalog.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>Varför kan jag inte åtgärda riskfyllda B2B-samarbetsanvändare i min katalog?

Riskbedömningen och reparationen för B2B-användare sker i deras arbetskatalog. På grund av detta visas inte gästanvändarna i rapporten riskfyllda användare i resurskatalogen och administratörer i resurskatalogen kan inte tvinga fram en säker återställning av lösenord för dessa användare.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>Vad gör jag om en B2B-samarbetsanvändare har blockerats på grund av en riskbaserad princip i min organisation?

Om en riskabel B2B-användare i katalogen blockeras av din riskbaserade policy måste användaren åtgärda den risken i sin arbetskatalog. Användare kan åtgärda sin risk genom att utföra en säker återställning av lösenord i sin hemkatalog. Om de inte har aktiverat lösenordsåterställning för självbetjäning i sin hemkatalog måste de kontakta sin egen organisations IT-personal för att en administratör ska kunna avvisa sin risk manuellt eller återställa sitt lösenord.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>Hur förhindrar jag att B2B-samarbetsanvändare påverkas av riskbaserade policyer?

Exklusive B2B-användare från organisationens riskbaserade principer för villkorlig åtkomst förhindrar att B2B-användare påverkas eller blockeras av sin riskbedömning. Om du vill utesluta dessa B2B-användare skapar du en grupp i Azure AD som innehåller alla organisationens gästanvändare. Lägg sedan till den här gruppen som ett undantag för dina inbyggda användarrisk- och inloggningsriskprinciper för identitetsskydd samt eventuella principer för villkorlig åtkomst som använder inloggningsrisk som ett villkor.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](../b2b/what-is-b2b.md)
