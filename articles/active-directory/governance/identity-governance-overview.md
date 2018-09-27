---
title: Azure AD-identitetsstyrning | Microsoft Docs
description: Azure AD-identitetsstyrning gör att du kan balansera din organisations behov av säkerhet och anställdas produktivitet med rätt processer och synlighet.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.openlocfilehash: 20b1c8673bfdb3b2207ed63749f79539c396642c
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47168038"
---
# <a name="what-is-azure-ad-identity-governance"></a>Vad är Azure AD-identitetsstyrning?

Styrning i Azure Active Directory (Azure AD) identity möjligt att balansera din organisations behov av säkerhet och anställdas produktivitet med rätt processer och synlighet. Den ger dig med funktioner för att se till att rätt användare har rätt åtkomst till rätt resurser, och du kan skydda, övervaka och granska åtkomst till viktiga tillgångar--samtidigt anställdas produktivitet.  

Identitetsstyrning ger organisationer möjlighet att göra följande i anställda, affärspartner och leverantörer, och tjänster och program:

- Styr användaridentiteternas livscykel
- Styra åtkomst livscykel
- Säker administration

Mer specifikt är den avsedd att hjälpa företag att åtgärda dessa fyra viktiga frågor:

- Vilka användare ska ha åtkomst till vilka resurser?
- Vad användarna gör med som har åtkomst till?
- Finns det effektiva organisationens kontroller för att hantera åtkomst?
- Kan verifiera granskare att kontrollerna fungerar?

## <a name="identity-lifecycle"></a>Identitetslivscykel

Identitetsstyrning hjälper organisationer att få en balans mellan *produktivitet* -hur snabbt kan en person har tillgång till resurserna de behöver, till exempel när de ansluta till min organisation? Och *security* – hur bör deras åtkomst ändras med tiden, till exempel på grund av ändringar till den personens anställningsstatus?  Identitetslivcykelhantering är grunden för Identitetshantering och effektiva styrning i stor skala kräver modernisering av hanteringsinfrastrukturen identitet livscykel för program.

Många organisationer är användaridentiteternas livscykel för anställda kopplad till en representation av användaren i ett system för HCM-system (human capital management).  Azure AD Premium har automatiskt användaridentiteter för personer som representeras i Workday i både Active Directory och Azure Active Directory, enligt beskrivningen i den [Workday-inkommande etablering (förhandsversion) självstudien](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium omfattar även [Microsoft Identity Manager](/microsoft-identity-manager/), vilket kan importera poster från lokala HCM-system, till exempel SAP, Oracle eBusiness och Oracle PeopleSoft.

Scenarier kräver mer och mer med hjälp av personer utanför organisationen. [Azure AD B2B](/azure/active-directory/b2b/) samarbete gör det möjligt att på ett säkert sätt dela din organisations program och tjänster med gästanvändare och externa partners från alla organisationer, samtidigt som de behåller kontrollen över företagets data.

## <a name="access-lifecycle"></a>Livscykel för åtkomst

Organisationer behöver en process för att hantera åtkomst utöver vad har etablerats för en användare när användarens identitet har skapats.  Dessutom måste företag för att kunna skala effektivt för att kunna utveckla och tillämpa principen för åtkomst och kontroller med jämna mellanrum.

Vanligtvis IT delegerar åtkomst till godkännande beslut och beslutsfattare.  Dessutom IT kan omfatta användare själva.  Till exempel behöver användare som har åtkomst till konfidentiell kundinformation i ett företags marknadsföring program i Europa veta företagets principer. Gästanvändare kan vara ovetande om hantering av kraven för data i en organisation som de har bjudits in.

Organisationer kan automatisera processen för åtkomst-livscykeln via tekniker som [dynamiska grupper](../users-groups-roles/groups-dynamic-membership.md), kopplade med användaretablering för [SaaS-appar](../saas-apps/tutorial-list.md) eller [appar som är integrerad med SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  Organisationer kan också styra som [gästanvändare har åtkomst till lokala program](../b2b/hybrid-cloud-to-on-premises.md).  Dessa komma åt rättigheter kan sedan granskas regelbundet med återkommande [Azure AD-åtkomstgranskningar](access-reviews-overview.md).

När en användare försöker få åtkomst till program, Azure AD tillämpar [villkorlig åtkomst](/azure/active-directory/conditional-access/) principer. Principer för villkorlig åtkomst kan exempelvis innehålla visar en [användningsvillkoren](active-directory-tou.md) och [att se till att användaren har samtyckt till att dessa villkor](../conditional-access/require-tou.md) innan den kan komma åt ett program.

## <a name="privileged-access-lifecycle"></a>Livscykel för privilegierad åtkomst

Historiskt sett har privilegierad åtkomst beskrivits av andra leverantörer som en separat funktion från identitetsstyrning. På Microsoft tror vi dock för privilegierad åtkomst är en viktig del av identitetsstyrning, särskilt för de angivna potentialen missbruk som associeras med dessa administratören rättigheter kan orsaka att en organisation. De anställda, leverantörer och leverantörer som överta för administrativa rättigheter kan du behöva styrs.

Azure AD Privileged Identity Management (PIM) ger ytterligare kontroller som är skräddarsydda för att skydda åtkomsten rights för resurser i Azure AD, Azure och andra Microsoft Online Services.  Just-in-time-åtkomst och ändring av aviseringsfunktioner som tillhandahålls av Azure AD PIM förutom multifaktorautentisering och villkorlig åtkomst tillhandahålla en bred uppsättning styrning kontroller för att skydda företagets resurser (directory, Office 365 och Azure-resursroller). Organisationer kan använda åtkomstgranskningar för att konfigurera återkommande åtkomst ny certifiering i enlighet för alla användare i administratörsroller precis som med andra former av åtkomst.

## <a name="getting-started"></a>Komma igång

Även om det finns ingen perfekt lösning eller en rekommendation för varje kund, tillhandahåller följande konfigurationer en guide till vilka baslinjen principer som Microsoft rekommenderar för att säkerställa en säkrare och mer produktiva personal.

- [Konfigurationer för identitets- och Enhetsåtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Säker privilegierad åtkomst](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>Åtkomstgranskningar

- [Vad är en åtkomstgranskning?](access-reviews-overview.md)
- [Hantera användarnas åtkomst med åtkomstgranskningar](manage-user-access-with-access-reviews.md)
- [Hantera gäståtkomst med åtkomstgranskningar](manage-guest-access-with-access-reviews.md)
- [Starta en åtkomstgranskning av en katalogroll](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>Användningsvillkor

- [Vad kan jag göra med användningsvillkoren?](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged identity management

- [Vad är Azure AD PIM?](../privileged-identity-management/pim-configure.md)
