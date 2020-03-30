---
title: Säkerhetsöversikt över Azure Active Directory Identity Protection
description: Läs om hur översikten över säkerhet ger dig en inblick i organisationens säkerhetsposition.
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
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382183"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Identitetsskydd för Azure Active Directory – säkerhetsöversikt

[Översikten Över säkerhet](https://aka.ms/IdentityProtectionRefresh) i Azure-portalen ger dig en inblick i organisationens säkerhetsposition. Det hjälper till att identifiera potentiella attacker och förstå effektiviteten i dina policyer.

"Säkerhetsöversikten" är i stort sett uppdelad i två avsnitt:

- Trender till vänster ger en tidslinje över risker i din organisation.
- Paneler till höger lyfter fram de viktigaste pågående problemen i organisationen och föreslår hur du snabbt vidtar åtgärder.

![Säkerhetsöversikt](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>Nya riskfyllda användare upptäckta

Det här diagrammet visar antalet nya riskfyllda användare som upptäcktes under den valda tidsperioden. Du kan filtrera visningen av det här diagrammet efter användarrisknivå (låg, medelhög, hög). Hovra över UTC-datumstegen för att se antalet riskfyllda användare som upptäckts för den dagen. Ett klick på detta diagram kommer att ta dig till "Risky användare" rapport. Om du vill åtgärda användare som är i riskzonen kan du överväga att ändra lösenordet.

### <a name="new-risky-sign-ins-detected"></a>Nya riskfyllda inloggningar har upptäckts

Det här diagrammet visar antalet riskfyllda inloggningar som upptäckts under den valda tidsperioden. Du kan filtrera visningen av det här diagrammet efter inloggningsrisktypen (i realtid eller aggregerad) och inloggningsrisknivån (låg, medelhög, hög). Oskyddade inloggningar är framgångsrika risk inloggningar i realtid som inte var MFA utmanas. (Obs: Inloggningar som är riskfyllda på grund av offlineidentifieringar kan inte skyddas i realtid av inloggningsriskprinciper). Hovra över UTC-datumstegen för att se antalet inloggningar som upptäckts i riskzonen för den dagen. Ett klick på detta diagram tar dig till rapporten "Risky sign-ins".

## <a name="tiles"></a>Ikoner
 
### <a name="high-risk-users"></a>Högriskanvändare

Panelen "Högriskanvändare" visar det senaste antalet användare med hög sannolikhet för identitetskompromiss. Dessa bör ha högsta prioritet för undersökningen. Ett klick på panelen "Högriskanvändare" omdirigeras till en filtrerad vy av rapporten "Risky users" som bara visar användare med en hög risknivå. Med den här rapporten kan du läsa mer och åtgärda dessa användare med en återställning av lösenord.

![Säkerhetsöversikt](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Användare av medelhög risk
Panelen "Medelriskanvändare" visar det senaste antalet användare med medelsannolikhet för identitetskompromiss. Ett klick på panelen "Medel riskanvändare" omdirigeras till en filtrerad vy av rapporten "Risky users" som endast visar användare med en risknivå på mediet. Med den här rapporten kan du undersöka och åtgärda dessa användare ytterligare.

### <a name="unprotected-risky-sign-ins"></a>Oskyddade riskfyllda inloggningar

Panelen "Oskyddade riskfyllda inloggningar" visar förra veckans antal framgångsrika riskfyllda inloggningar i realtid som inte blockerades eller MFA ifrågasattes av en policy för villkorlig åtkomst, riskprincip för identitetsskydd eller mfa per användare. Dessa är potentiellt komprometterade inloggningar som var framgångsrika och inte MFA utmanas. Använd en inloggningsriskpolicy för att skydda sådana inloggningar i framtiden. Ett klick på panelen Oskyddade riskfyllda inloggningar omdirigeras till konfigurationsbladet för inloggningsriskprincip där du kan konfigurera inloggningsriskprincipen så att den kräver MFA på en inloggning med en angiven risknivå.

### <a name="legacy-authentication"></a>Äldre autentisering

Panelen Äldre autentisering visar den senaste veckans antal äldre autentiseringar i organisationen. Äldre autentiseringsprotokoll stöder inte moderna säkerhetsmetoder som en MFA. Om du vill förhindra äldre autentisering kan du använda en princip för villkorlig åtkomst. Ett klick på panelen "Äldre autentisering" omdirigerar dig till "Identity Secure Score".

### <a name="identity-secure-score"></a>Identitetssäker poäng

Identity Secure Score mäter och jämför din säkerhetsposition med branschmönster. Om du klickar på panelen "Identity Secure Score (Preview)" omdirigeras den till bladet "Identity Secure Score" där du kan lära dig mer om hur du förbättrar din säkerhetshållning.

## <a name="next-steps"></a>Nästa steg

- [Vad är risk](concept-identity-protection-risks.md)

- [Policyer tillgängliga för att minska riskerna](concept-identity-protection-policies.md)
