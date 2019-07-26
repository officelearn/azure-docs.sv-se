---
title: Azure Active Directory (Azure AD) säkerhets översikt för identitets skydd | Microsoft Docs
description: Lär dig hur säkerhets översikten ger dig en inblick i din organisations säkerhets position.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b894f7020083dd6ca46c394ec2930a3da36bc76d
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335204"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Översikt över Azure Active Directory Identity Protection-säkerhet

[Säkerhets översikten](https://aka.ms/IdentityProtectionRefresh) ger dig en inblick i din organisations säkerhets position. Det hjälper dig att identifiera potentiella attacker och förstå effektiviteten hos dina principer.

Säkerhets översikten är i stort sett uppdelad i två delar:

- Trender, till vänster, ger en tids linje med risk i din organisation.
- Panelerna till höger markerar du de viktiga pågående problemen i din organisation och föreslår hur du snabbt vidtar åtgärder.

![Säkerhetsöversikt](./media/security-overview/01.png)
  
## <a name="trends"></a>Trender

### <a name="new-risky-users-detected"></a>En ny riskanvändare har identierats

Det här diagrammet visar antalet nya riskfyllda användare som identifierades under den valda tids perioden. Du kan filtrera visningen av det här diagrammet efter användar risk nivå (låg, medium, hög). Hovra över UTC-datumets steg för att se antalet riskfyllda användare som har identifierats för den dagen. Om du klickar på det här diagrammet visas rapporten riskfyllda användare. Överväg att ändra lösen ordet för att reparera användare som är utsatta för risk.

### <a name="new-risky-sign-ins-detected"></a>En ny riskfylld inloggning har identierats

Det här diagrammet visar antalet riskfyllda inloggningar som har identifierats under den valda tids perioden. Du kan filtrera visningen av det här diagrammet efter inloggnings risk typ (i real tid eller mängd) och inloggnings risk nivån (låg, medium, hög). Oskyddade inloggningar har slutförts i real tids risk inloggningar som inte är MFA-anropade. (Obs: Inloggningar som är riskfyllda på grund av offline-identifiering kan inte skyddas i real tid genom inloggnings risk principer. Hovra över UTC-datumen för att se hur många inloggningar som har identifierats vid risk för den dagen. Om du klickar på det här diagrammet visas rapporten "riskfyllda inloggningar".

## <a name="tiles"></a>Sida vid sida
 
### <a name="high-risk-users"></a>Högriskanvändare

Panelen "hög risk användare" visar det senaste antalet användare med hög sannolikhet för identitets kompromisser. Dessa bör vara en högsta prioritet för undersökning. Ett klick på panelen "hög risk användare" kommer att omdirigeras till en filtrerad vy av rapporten "riskfyllda användare" som endast visar användare med risk nivån hög. Med den här rapporten kan du läsa mer och åtgärda dessa användare med lösen ords återställning.

![Säkerhetsöversikt](./media/security-overview/02.png)

### <a name="medium-risk-users"></a>Mellanriskanvändare
Panelen "medel risk användare" visar det senaste antalet användare med medelhög sannolikhet för identitets kompromisser. Ett klick på panelen "medelhög risk användare" omdirigeras till en filtrerad vy av rapporten "riskfyllda användare" som endast visar användare med risk nivån medel. Med hjälp av den här rapporten kan du ytterligare undersöka och åtgärda dessa användare.

### <a name="unprotected-risky-sign-ins"></a>Oskyddade riskfyllda inloggningar

Panelen "oskyddade riskfyllda inloggningar" visar den senaste veckan antal lyckade inloggningar i real tid som varken blockerades eller MFA har anropats av en princip för villkorlig åtkomst, identitets skydds princip eller användarspecifika MFA. Detta är potentiellt komprometterade inloggningar som har lyckats och inte MFA-anrop. Använd en princip för inloggnings risker för att skydda sådana inloggningar i framtiden. Ett klick på panelen oskyddade riskfyllda inloggningar omdirigeras till bladet konfiguration av inloggnings risk princip där du kan konfigurera inloggnings risk principen så att den kräver MFA på en inloggning med en angiven risk nivå.

### <a name="legacy-authentication"></a>Äldre autentisering

Panelen "äldre autentisering" visar den senaste veckans antal äldre autentiseringar i din organisation. Äldre autentiseringsprotokoll stöder inte modern säkerhets metoder, till exempel ett MFA. Du kan använda en princip för villkorlig åtkomst för att förhindra äldre autentisering. Klicka på panelen "äldre autentisering" för att omdirigera dig till "Identity Secure Scores".

### <a name="identity-secure-score"></a>Identitetsäkerhetspoäng

Mät värden för identiteten säker Poäng och jämför din säkerhets position med bransch mönster. Om du klickar på panelen identitets säkra poäng (förhands granskning) omdirigeras du till bladet "identitets säker Poäng (för hands version)" där du kan lära dig mer om hur du kan förbättra din säkerhets position.

## <a name="next-steps"></a>Nästa steg

- [Kanal 9: Azure AD och identitet visar: För hands version av identitets skydd](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Aktivera Azure Active Directory Identity Protection](enable.md)
