---
title: Identitetsstyrning - Azure Active Directory | Microsoft-dokument
description: Med Azure Active Directory Identity Governance kan du balansera organisationens behov av säkerhet och medarbetarnas produktivitet med rätt processer och synlighet.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063687"
---
# <a name="what-is-azure-ad-identity-governance"></a>Vad är Azure AD Identity Governance?

Azure Active Directory (Azure AD) Identity Governance kan du balansera din organisations behov av säkerhet och anställdas produktivitet med rätt processer och synlighet. Det ger dig möjlighet att se till att rätt personer har rätt tillgång till rätt resurser. Med de här och relaterade funktionerna Azure AD och Enterprise Mobility + Security kan du minska åtkomstrisken genom att skydda, övervaka och granska åtkomst till kritiska tillgångar – samtidigt som du säkerställer anställdas och affärspartners produktivitet.  

Identitetsstyrning ger organisationer möjlighet att utföra följande uppgifter bland anställda, affärspartners och leverantörer och mellan tjänster och program både lokalt och i moln:

- Styr identitetslivscykeln
- Styra åtkomstlivscykeln
- Säker privilegierad åtkomst för administration

Specifikt är det avsett att hjälpa organisationer att ta itu med dessa fyra nyckelfrågor:

- Vilka användare ska ha åtkomst till vilka resurser?
- Vad gör dessa användare med den åtkomsten?
- Finns det effektiva organisationskontroller för hantering av åtkomst?
- Kan revisorer kontrollera att kontrollerna fungerar?

## <a name="identity-lifecycle"></a>Identitetslivscykel

Identitetsstyrning hjälper organisationer att uppnå en balans mellan *produktivitet* – Hur snabbt kan en person ha tillgång till de resurser de behöver, till exempel när de går med i min organisation? Och *säkerhet* - Hur ska deras tillgång förändras med tiden, till exempel på grund av förändringar i den personens anställningsstatus?  Identitetslivscykelhantering är grunden för identitetsstyrning, och effektiv styrning i stor skala kräver modernisering av identitetslivscykelhanteringsinfrastrukturen för program.

![Identitetslivscykel](./media/identity-governance-overview/identity-lifecycle.png)

För många organisationer är identitetslivscykel för anställda knuten till representationen av den användaren i ett HCM-system (human capital management).  Azure AD Premium underhåller automatiskt användaridentiteter för personer som är representerade i Workday i både Active Directory och Azure Active Directory, enligt beskrivningen i [självstudiekursen Arbetsdags inkommande etablering](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium innehåller även [Microsoft Identity Manager](/microsoft-identity-manager/), som kan importera poster från lokala HCM-system som SAP, Oracle eBusiness och Oracle PeopleSoft.

Scenarier kräver i allt högre grad samarbete med personer utanför organisationen. [Azure AD B2B-samarbete](/azure/active-directory/b2b/) gör att du kan på ett säkert sätt dela organisationens program och tjänster med gästanvändare och externa partner från alla organisationer, samtidigt som du behåller kontrollen över dina egna företagsdata.  [Azure AD-berättigandehantering](entitlement-management-overview.md) gör att du kan välja vilken organisations användare som får begära åtkomst och läggas till som B2B-gäster i organisationens katalog och ser till att dessa gäster tas bort när de inte längre behöver åtkomst.

## <a name="access-lifecycle"></a>Livscykel för åtkomst

Organisationer behöver en process för att hantera åtkomst utöver vad som ursprungligen etablerades för en användare när användarens identitet skapades.  Dessutom måste företagsorganisationer kunna skalas effektivt för att kunna utveckla och genomdriva åtkomstpolicy och kontroller kontinuerligt.

![Livscykel för åtkomst](./media/identity-governance-overview/access-lifecycle.png)

Vanligtvis får IT-ombud åtkomst till godkännandebeslut till beslutsfattare för företag.  Dessutom kan IT involvera användarna själva.  Användare som har åtkomst till konfidentiella kunddata i ett företags marknadsföringsprogram i Europa behöver till exempel känna till företagets policyer. Gästanvändare kan vara omedvetna om hanteringskraven för data i en organisation som de har bjudits in till.

Organisationer kan automatisera åtkomstlivscykelprocessen med hjälp av tekniker som [dynamiska grupper,](../users-groups-roles/groups-dynamic-membership.md)tillsammans med användaretablering till [SaaS-appar](../saas-apps/tutorial-list.md) eller [appar som är integrerade med SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organisationer kan också styra vilka [gästanvändare](../b2b/hybrid-cloud-to-on-premises.md)som har åtkomst till lokala program .  Dessa åtkomsträttigheter kan sedan granskas regelbundet med hjälp av återkommande [Azure AD-åtkomstgranskningar](access-reviews-overview.md).   [Azure AD-berättigandehantering](entitlement-management-overview.md) kan du också definiera hur användare begär åtkomst över paket med grupp- och gruppmedlemskap, programroller och SharePoint Online-roller.

När en användare försöker komma åt program tillämpar Azure AD principer [för villkorlig åtkomst.](/azure/active-directory/conditional-access/) Principer för villkorlig åtkomst kan till exempel omfatta visning [av användningsvillkor](../conditional-access/terms-of-use.md) och [se till att användaren har godkänt dessa villkor](../conditional-access/require-tou.md) innan han eller hon kan komma åt ett program.

## <a name="privileged-access-lifecycle"></a>Livscykel för privilegierad åtkomst

Historiskt sett har privilegierad åtkomst beskrivits av andra leverantörer som en separat funktion från identitetsstyrning. På Microsoft anser vi dock att det är en viktig del av identitetsstyrningen att styra privilegierad åtkomst – särskilt med tanke på risken för missbruk som är associerat med dessa administratörsrättigheter kan orsaka en organisation. De medarbetare, leverantörer och entreprenörer som tar på sig administrativa rättigheter måste styras.

![Livscykel för privilegierad åtkomst](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) innehåller ytterligare kontroller som är skräddarsydda för att skydda åtkomsträttigheter för resurser, i Azure AD, Azure och andra Microsoft Online-tjänster.  Åtkomsten för just-in-time- och rolländringsaviseringar som tillhandahålls av Azure AD PIM, förutom multifaktorautentisering och villkorlig åtkomst, ger en omfattande uppsättning styrningskontroller som hjälper till att skydda företagets resurser (katalog, Office 365 och Azure-resursroller). Precis som med andra former av åtkomst kan organisationer använda åtkomstgranskningar för att konfigurera återkommande åtkomstcertifiering för alla användare i administratörsroller.

## <a name="getting-started"></a>Komma igång

Kolla in fliken Komma igång med **identitetsstyrning** i Azure-portalen för att börja använda rättighetshantering, åtkomstgranskningar, privilegierad identitetshantering och användningsvillkor.

![Identitetsstyrning har startat](./media/identity-governance-overview/getting-started.png)


Om du har några synpunkter på funktioner för identitetsstyrning klickar du på **Få feedback i** Azure-portalen för att skicka din feedback. Teamet granskar regelbundet din feedback.

Det finns ingen perfekt lösning eller rekommendation för varje kund, men följande konfigurationsguider innehåller också de grundläggande principer som Microsoft rekommenderar att du följer för att säkerställa en säkrare och mer produktiv arbetsstyrka.

- [Konfigurationer för identitets- och enhetsåtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Säker privilegierad åtkomst](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Tillägg - minst privilegierade roller för hantering av identitetsstyrningsfunktioner

Det är en bra idé att använda den minst privilegierade rollen för att utföra administrativa uppgifter i identitetsstyrning. Vi rekommenderar att du använder Azure AD PIM för att aktivera en roll som behövs för att utföra dessa uppgifter. Följande är de minst privilegierade katalogrollerna för att konfigurera identitetsstyrningsfunktioner:

| Funktion | Minst privilegierad roll |
| ------- | --------------------- |
| Berättigandehantering | Användaradministratör (med undantag för att lägga till SharePoint Online-webbplatser i kataloger, vilket kräver global administratör) |
| Åtkomstgranskningar | Användaradministratör (med undantag för åtkomstgranskningar av Azure- eller Azure AD-roller, vilket kräver administratör för privilegierad roll) |
|Privileged Identity Management | Administratör för privilegierad roll |
| Användningsvillkor | Säkerhetsadministratör eller administratör för villkorlig åtkomst |

## <a name="next-steps"></a>Nästa steg

- [Vad är berättigandehantering i Azure AD?](entitlement-management-overview.md)
- [Vad är Azure AD-åtkomstgranskningar?](access-reviews-overview.md)
- [Vad är Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Vad kan jag göra med användningsvillkoren?](active-directory-tou.md)


