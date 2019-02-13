---
title: Säkerhetsöversikt för Azure Active Directory (Azure AD) Identity Protection | Microsoft Docs
description: Lär dig hur Security översikt ger dig en överblick över säkerhetstillståndet för din organisation.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4026f710def26b50da87ebb8fdafcafc74d84758
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185833"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection - Säkerhetsöversikt

Security översikt ger dig en överblick över säkerhetstillståndet för din organisation. Det hjälper dig identifiera potentiella attacker och förstå effektiviteten i dina principer.

Security översikt är brett uppdelad i två delar:

- Trender, ger vänster, en tidslinje för risk i din organisation.

- Paneler till höger, markera viktiga pågående problem i din organisation och föreslå hur snabbt vidta åtgärder.


![Säkerhetsöversikt](./media/security-overview/01.png)
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>En ny riskanvändare har identierats

Det här diagrammet visar hur många nya riskfylld användare som identifierades under den valda tidsperioden. Du kan filtrera visningen av det här diagrammet genom att användaren risknivå (låg, medelhög och hög). Hovra över steg för UTC-datum att se hur många riskfylld användare som har identifierats för den dagen. Klicka på det här diagrammet kommer du till rapporten ”riskfylld användare”. Om du vill åtgärda användare i farozonen, Överväg att ändra sina lösenord.

### <a name="new-risky-sign-ins-detected"></a>En ny riskfylld inloggning har identierats

Det här diagrammet visar antalet riskfyllda inloggningar som har identifierats under den valda tidsperioden. Du kan filtrera visningen av det här diagrammet efter inloggningsrisk-typ (i realtid eller sammanställd) och logga in risknivån (låg, medelhög och hög). Oskyddade inloggningar är lyckad i realtid risk inloggningar som inte är MFA Avvisad. (Obs: Sign-INS som kan utgöra en risk på grund av offline identifieringar kan inte skyddas i realtid av inloggningsrisk principer). Hovra över steg för UTC-datum att se antalet inloggningar som har identifierats i fara för den dagen. Klicka på det här diagrammet kommer du till rapporten 'Riskfyllda inloggningar ”.

## <a name="tiles"></a>Sida vid sida
 
### <a name="high-risk-users"></a>Högriskanvändare

Panelen ”hög risk användare” visar senaste antal användare med hög sannolikhet att identitet avslöjas. Bör vara hög prioritet för undersökning. Ett klick på panelen ”hög risk användare” omdirigerar till en filtrerad vy av ”riskfylld användare”-rapport som visar endast användare med en risknivå hög. Med den här rapporten kan du läsa mer och åtgärda dessa användare med en återställning av lösenord.

![Säkerhetsöversikt](./media/security-overview/02.png)


### <a name="medium-risk-users"></a>Mellanriskanvändare
Panelen ”Medelrisk-användare” visar senaste antal användare med medelhög sannolikheten för identitet har komprometterats. Klicka på ”Medelrisk-användare”-ikonen omdirigerar till en filtrerad vy av ”riskfylld användare”-rapport som visar endast användare med en risknivå medium. Med den här rapporten kan du ytterligare undersöka och åtgärda dessa användare.

### <a name="unprotected-risky-sign-ins"></a>Oskyddade riskfyllda inloggningar

Panelen ”oskyddade riskfyllda inloggningar” visar den senaste veckan antal lyckades, i realtid över riskfyllda inloggningar som inte har blockerats eller MFA på samma princip för villkorlig åtkomst, identitetsskydd riskprincipen och MFA per användare. Här är potentiellt komprometterade inloggningar som har genomförts och inte MFA på samma. Tillämpa en princip för inloggningsrisk för att skydda dessa inloggningar i framtiden. Klicka på ”oskyddade riskfyllda inloggningar” panel omdirigerar till inloggningsrisk principbladet där du kan konfigurera princip för inloggningsrisk att kräva MFA för en inloggning med en angiven risknivå.


### <a name="legacy-authentication"></a>Äldre autentisering

Panelen ”äldre autentisering” visar den senaste veckan uppräkning av äldre autentiseringar i din organisation. Äldre autentiseringsprotokoll stöder inte moderna säkerhetsmetoder, till exempel en MFA. Du kan tillämpa principer för villkorlig åtkomst för att förhindra att äldre autentisering. Klicka på panelen för ”äldre autentisering” omdirigera dig till 'Identity skydda poäng ”.


### <a name="identity-secure-score"></a>Säker identitets-poäng

Identitet skyddar Score mäter och jämför din säkerhetsposition till bransch-mönster. Om du klickar på 'Identity skydda poäng (förhandsversion), sida vid sida, kommer den omdirigera till bladet 'Identity skydda poäng (förhandsversion), där du kan lära dig mer om att förbättra din säkerhetsposition.


## <a name="next-steps"></a>Nästa steg

- [Channel 9: Azure AD och Identity visa: Förhandsversionen av Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Aktivera Azure Active Directory Identity Protection](enable.md)

