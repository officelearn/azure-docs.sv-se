---
title: Identitets styrning – Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance gör det möjligt att balansera din organisations behov av säkerhet och produktivitet i anställda med rätt processer och synlighet.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb7ab566d861e0c4256acda8f8facd1e94d4ad6
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87796785"
---
# <a name="what-is-azure-ad-identity-governance"></a>Vad är Azure AD Identity Governance?

Med identitets styrning i Azure Active Directory (Azure AD) kan du balansera din organisations behov av säkerhet och produktivitet för anställda med rätt processer och synlighet. Det ger dig möjlighet att se till att rätt personer har rätt åtkomst till rätt resurser. Med dessa och relaterade funktioner i Azure AD och Enterprise Mobility + Security kan du minska åtkomst risken genom att skydda, övervaka och granska åtkomst till kritiska till gångar – samtidigt som du säkerställer produktiviteten för anställda och affärs partner.  

Identitets styrning ger organisationer möjlighet att utföra följande uppgifter mellan anställda, affärs partner och leverantörer och mellan tjänster och program både lokalt och i molnet:

- Styr identitets livs cykeln
- Styra åtkomst livs cykeln
- Skydda privilegie rad åtkomst för administration

Särskilt är det avsett att hjälpa organisationer att lösa dessa fyra viktiga frågor:

- Vilka användare ska ha åtkomst till vilka resurser?
- Vilka användare gör med den här åtkomsten?
- Finns det effektiva organisations kontroller för att hantera åtkomst?
- Kan granskare verifiera att kontrollerna fungerar?

## <a name="identity-lifecycle"></a>Identitets livs cykel

Identitets styrning hjälper organisationer att få en balans mellan *produktiviteten* – hur snabbt kan en person ha åtkomst till de resurser som de behöver, till exempel när de ansluter till min organisation? Och *säkerhet* – hur ska deras åtkomst ändras över tid, till exempel på grund av ändringar i personens anställnings status?  Hantering av identitets livs cykeln är grunden för identitets styrning, och effektiva styrningar i skalan kräver att infrastrukturen för identitets livs cykels hantering används för program.

![Identitets livs cykel](./media/identity-governance-overview/identity-lifecycle.png)

För många organisationer är identitets livs cykeln för anställda knuten till åter givning av användaren i ett HCM-system (Human kapitalet Management).  Azure AD Premium hanterar automatiskt användar identiteter för personer som representeras i Workday i både Active Directory och Azure Active Directory, enligt beskrivningen i [självstudien om inkommande etablering för arbets dagar](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium innehåller även [Microsoft Identity Manager](/microsoft-identity-manager/), som kan importera poster från lokala HCM-system som SAP, Oracle eBusiness och Oracle, Oracle.

I allt större utsträckning kräver scenarier samarbete med personer utanför organisationen. Med [Azure AD B2B](/azure/active-directory/b2b/) -samarbete kan du på ett säkert sätt dela din organisations program och tjänster med gäst användare och externa partner från vilken organisation som helst, samtidigt som du behåller kontrollen över dina egna företags data.  Med [hantering av Azure AD-hantering](entitlement-management-overview.md) kan du välja vilka organisations användare som tillåts begära åtkomst och läggas till som B2B-gäster till din organisations katalog och se till att dessa gäster tas bort när de inte längre behöver åtkomst.

## <a name="access-lifecycle"></a>Åtkomst livs cykel

Organisationer behöver en process för att hantera åtkomst utöver vad som ursprungligen etablerades för en användare när användarens identitet skapades.  Dessutom måste företags organisationer kunna skala effektivt för att kunna utveckla och upprätthålla åtkomst principer och kontroller kontinuerligt.

![Åtkomst livs cykel](./media/identity-governance-overview/access-lifecycle.png)

Vanligt vis delegerar den åtkomst till godkännande beslut till besluts fattare i företaget.  Dessutom kan de involvera själva användarna.  Till exempel behöver användare som har åtkomst till konfidentiell kund information i ett företags marknadsförings program i Europa känna till företagets principer. Gäst användare kan vara medvetna om hanterings kraven för data i en organisation som de har bjudits in till.

Organisationer kan automatisera livs cykel processen för Access genom tekniker som [dynamiska grupper](../users-groups-roles/groups-dynamic-membership.md), tillsammans med användar etablering till [SaaS appar](../saas-apps/tutorial-list.md) eller appar som är [integrerade med scim](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organisationer kan också styra vilka [gäst användare som har åtkomst till lokala program](../b2b/hybrid-cloud-to-on-premises.md).  Dessa behörigheter kan sedan regelbundet granskas med hjälp av återkommande [granskningar av Azure AD-åtkomst](access-reviews-overview.md).   [Hantering av Azure AD](entitlement-management-overview.md) -behörighet gör det också möjligt att definiera hur användare begär åtkomst över paket av grupp-och team medlemskap, program roller och SharePoint Online-roller.

När en användare försöker få åtkomst till program tvingar Azure AD sig att tillämpa principer för [villkorlig åtkomst](/azure/active-directory/conditional-access/) . Principer för villkorlig åtkomst kan till exempel innehålla ett [antal användnings villkor](../conditional-access/terms-of-use.md) och [Se till att användaren har godkänt dessa villkor innan de](../conditional-access/require-tou.md) kan komma åt ett program.

## <a name="privileged-access-lifecycle"></a>Livs cykel för privilegie rad åtkomst

Historiskt sett har privilegie rad åtkomst beskrivits av andra leverantörer som en separat funktion från identitets styrningen. Men på Microsoft tror vi att du styr privilegie rad åtkomst är en viktig del av identitets styrningen – särskilt om risken för missbruk som är kopplad till dessa administratörs rättigheter kan orsaka en organisation. De anställda, leverantörer och entreprenörer som tar på administrativa rättigheter måste regleras.

![Livs cykel för privilegie rad åtkomst](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) ger ytterligare kontroller som är anpassade för att skydda åtkomst rättigheter för resurser, i Azure AD, Azure och andra Microsoft Online Services.  Just-in-Time-åtkomst och roll ändrings aviseringar som tillhandahålls av Azure AD PIM, förutom Multi-Factor Authentication och villkorlig åtkomst, ger en omfattande uppsättning styrnings kontroller för att skydda företagets resurser (katalog, Office 365 och Azure resurs roller). Precis som med andra former av åtkomst kan organisationer använda åtkomst granskningar för att konfigurera återcertifiering av återkommande åtkomst för alla användare i administratörs roller.

## <a name="getting-started"></a>Komma igång

Kolla in fliken komma igång med **identitets styrning** i Azure Portal för att börja använda rättighets hantering, åtkomst granskningar, Privileged Identity Management och användningsvillkor.

![Komma igång med identitets styrning](./media/identity-governance-overview/getting-started.png)


Om du har feedback om funktioner för identitets styrning klickar du på **feedback?** i Azure Portal för att skicka in din feedback. Gruppen granskar regelbundet din feedback.

Även om det inte finns någon perfekt lösning eller rekommendation för varje kund, innehåller följande konfigurations guider även bas linje principer som Microsoft rekommenderar att du följer för att säkerställa en säkrare och produktiv personal.

- [Konfigurationer för identitets- och enhetsåtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Säker privilegierad åtkomst](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Bilaga-minst privilegierade roller för hantering i identitets styrnings funktioner

Vi rekommenderar att du använder den minst privilegierade rollen för att utföra administrativa uppgifter i identitets styrning. Vi rekommenderar att du använder Azure AD PIM för att aktivera en roll vid behov för att utföra dessa uppgifter. Följande är de minst privilegierade katalog rollerna för att konfigurera identitets styrnings funktioner:

| Funktion | Minst privilegie rad roll |
| ------- | --------------------- |
| Berättigandehantering | Användar administratör (med undantag för att lägga till SharePoint Online-webbplatser i kataloger som kräver global administratör) |
| Åtkomstgranskningar | Användar administratör (med undantag för åtkomst granskningar av Azure-eller Azure AD-roller, som kräver administratör för privilegierade roller) |
|Privileged Identity Management | Privilegie rad roll administratör |
| Villkor för användning | Säkerhets administratör eller administratör för villkorlig åtkomst |

## <a name="next-steps"></a>Nästa steg

- [Vad är berättigandehantering i Azure AD?](entitlement-management-overview.md)
- [Vad är Azure AD-åtkomstgranskningar?](access-reviews-overview.md)
- [Vad är Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Vad kan jag göra med användningsvillkoren?](active-directory-tou.md)


