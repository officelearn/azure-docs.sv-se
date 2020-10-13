---
title: Azure Security benchmark v2 – privilegie rad åtkomst
description: Azure Security benchmark v2 privilegie rad åtkomst
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 68fdff9444286a7f304c3a3361ad33a02e87a282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758378"
---
# <a name="security-control-v2-privileged-access"></a>Säkerhets kontroll v2: privilegie rad åtkomst

Privilegie rad åtkomst täcker kontroller för att skydda privilegie rad åtkomst till din Azure-klient och dina resurser. Detta inkluderar ett antal kontroller för att skydda din administrativa modell, administrativa konton och arbets stationer med privilegie rad åtkomst mot avsiktliga och oavsiktliga risker.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: skydda och begränsa privilegierade användare

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Begränsa antalet starkt privilegierade användar konton och skydda dessa konton på en utökad nivå. De viktigaste inbyggda rollerna i Azure AD är global administratör och administratör för privilegierade roller, eftersom användare som tilldelats dessa två roller kan delegera administratörs roller. Med dessa privilegier kan användare direkt eller indirekt läsa och ändra alla resurser i din Azure-miljö:

- Global administratör/företags administratör: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure AD, samt tjänster som använder Azure AD-identiteter.

- Privilegie rad roll administratör: användare med den här rollen kan hantera roll tilldelningar i Azure AD, samt i Azure AD Privileged Identity Management (PIM). Dessutom tillåter den här rollen hantering av alla aspekter av PIM och administrativa enheter.

Obs! Du kan ha andra viktiga roller som behöver regleras om du använder anpassade roller med vissa privilegierade behörigheter tilldelade. Och du kanske också vill använda liknande kontroller för administratörs kontot för kritiska företags till gångar.  

Du kan aktivera just-in-Time (JIT) privilegie rad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhets aviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

- [Administratörs roll behörigheter i Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Använd Azure Privileged Identity Management säkerhets aviseringar](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Säkerhets åtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-2 | 13,2, 2,10 | AC-2, SC-3, SC-7 |

Isolera åtkomst till verksamhets kritiska system genom att begränsa vilka konton som beviljas privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i. Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till dina affärs kritiska till gångar, till exempel Active Directory-domän controllers (DCs), säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar. 

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll. 

Se till att tilldela separata privilegierade konton som skiljer sig från de standard användar konton som används för e-post, surfning och produktivitets uppgifter.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanterings grupp](../../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: granska och Stäm av användar åtkomst regelbundet

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-3 | 4,1, 16,9, 16,10 | AC-2 |

Granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och åtkomst nivån är giltiga. Du kan använda åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa ett arbets flöde för åtkomst gransknings rapport som underlättar gransknings processen.
Dessutom kan Azure Privileged Identity Management konfigureras för avisering när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade. 

Obs! vissa Azure-tjänster har stöd för lokala användare och roller som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Använda Azure AD-identitets-och åtkomst granskningar](../../active-directory/governance/access-reviews-overview.md)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.
Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Säkerhets åtgärder (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.
- [Vad är åtkomst granskningar för Azure AD](../../active-directory/governance/access-reviews-overview.md) 

- [Vad är hantering av Azure AD-berättigande](../../active-directory/governance/entitlement-management-overview.md)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-6 | 4,6, 11,6, 12,12 | AC-2, SC-3, SC-7 |

Skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Använd hög säkra användar arbets stationer och/eller Azure-skydds för administrativa uppgifter. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och/eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer och begränsad logisk och nätverks åtkomst. 

- [Förstå arbets stationer med privilegie rad åtkomst](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuera en privilegie rad åtkomst arbets Station](../../active-directory/devices/howto-azure-managed-workstation.md)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Säkerhets åtgärder (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ bara tillräckligt med administration (princip för minsta behörighet)

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-7 | 14,6 | AC-2, AC-3, SC-3 |

Med rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du hantera Azure Resource Access via roll tilldelningar. Du kan tilldela dessa roller till användare, grupp tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell och Azure Portal. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Begränsade privilegier kompletterar JIT-metoden (just in Time) för Azure AD Privileged Identity Management (PIM) och dessa privilegier bör granskas regelbundet.
Använd inbyggda roller för att allokera behörighet och endast skapa en anpassad roll vid behov. 

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md)

- [Så här konfigurerar du Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

- [Använda Azure AD-identitets-och åtkomst granskningar](../../active-directory/governance/access-reviews-overview.md)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Välj godkännande process för Microsoft Support 

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

I support scenarier där Microsoft behöver åtkomst till kund information, Customer Lockbox ger en möjlighet att uttryckligen granska och godkänna eller avvisa varje begäran om kund data åtkomst.

- [Förstå Customer Lockbox](../fundamentals/customer-lockbox-overview.md)

**Ansvar**: kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetskompatibilitet](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

