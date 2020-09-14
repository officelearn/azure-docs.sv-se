---
title: Azure Security benchmark v2 – privilegie rad åtkomst
description: Azure Security benchmark v2 privilegie rad åtkomst
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059435"
---
# <a name="security-control-privileged-access"></a>Säkerhets kontroll: privilegie rad åtkomst

Privilegie rad åtkomst täcker kontroller för att skydda privilegie rad åtkomst till din Azure-klient och dina resurser. Detta inkluderar ett antal kontroller för att skydda din administrativa modell, administrativa konton och arbets stationer med privilegie rad åtkomst mot avsiktliga och oavsiktliga risker.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: skydda och begränsa globala administratörer

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Användare som har tilldelats rollen som global administratör kan läsa och ändra alla administrativa inställningar i din Azure AD-organisation. Begränsa antalet globala Azure-administratörskonton till högst två för varje prenumeration. De mest kritiska inbyggda rollerna i Azure AD är global administratör och den privilegierade roll administratören när användare som tilldelats dessa två roller kan delegera administratörs roller:
- Global administratör/företags administratör: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure AD, samt tjänster som använder Azure AD-identiteter.

- Privilegie rad roll administratör: användare med den här rollen kan hantera roll tilldelningar i Azure AD, samt i Azure AD Privileged Identity Management (PIM). Dessutom tillåter den här rollen hantering av alla aspekter av PIM och administrativa enheter.

Obs! Du kan ha andra viktiga roller som behöver regleras om du använder anpassade roller med vissa privilegierade behörigheter tilldelade. Och du kanske också vill använda liknande kontroller för administratörs kontot för kritiska företags till gångar.  

Du kan aktivera just-in-Time (JIT) privilegie rad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhets aviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

Obs! vissa Azure-tjänster som Azure SQL stöder lokal användarautentisering förutom Azure AD-autentiseringen. Den här typen av lokal användarautentisering hanteras inte via Azure AD. Du måste hantera dessa användare separat.

- [Administratörs roll behörigheter i Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md)

- [Använd Azure Privileged Identity Management säkerhets aviseringar](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Använd Azure Security Center för att övervaka identitet och åtkomst](../../security-center/security-center-identity-access.md)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Identitet och nycklar](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: granska och Stäm av användar åtkomst regelbundet

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-2 | 4,1, 16,9, 16,10 | AC-2 |

Granska användar konton och åtkomst rättigheter regelbundet för att se till att användar kontona och deras åtkomst är giltiga. 

Använd Azure AD-identitets-och åtkomst granskningar för att hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

För administrativa användare på Azure-tjänsten och arbets belastnings nivån bör en mer frekvent användar-och åtkomst granskning utföras. Du kan också använda Azure Privileged Identity Management säkerhets aviseringar för att identifiera när administratörs konton skapas och för att hitta administratörs konton som är inaktuella eller felaktigt konfigurerade. 

Obs! vissa Azure-tjänster, till exempel Azure SQL, stöder lokala användare som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Använd Azure Security Center för att övervaka identitet och åtkomst](../../security-center/security-center-identity-access.md)

- [Använd Azure Privileged Identity Management säkerhets aviseringar](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Justera administrativt ansvar över team](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Förstå Azure AD repor ting](/azure/active-directory/reports-monitoring/)

- [Använda Azure AD-identitets-och åtkomst granskningar](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management – granska åtkomst till Azure AD-roller](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure Security Center-övervaka identitet och åtkomst](../../security-center/security-center-identity-access.md)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Identitet och nycklar](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetsefterlevnad](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: Konfigurera ett konto för nöd åtkomst i Azure AD

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-3 | 12,3 | AC-2 |

För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Identitet och nycklar](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetsefterlevnad](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Säkerhetsåtgärdscenter](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: automatisera arbets flödet för Azure Identity and Access-begäran

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-4 | E.t. | AC-2, AC-5, PM-10 |

Använd funktioner för hantering av Azure AD-behörighet för att automatisera Azure-arbetsflödes arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.  

- [Vad är hantering av Azure AD-berättigande](../../active-directory/governance/entitlement-management-overview.md)

- [Konfigurera åtkomst förfrågan och godkännande processen](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Identitet och nycklar](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetsefterlevnad](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: Använd hög säkra datorer för administrativa uppgifter

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-5 | 4,6, 11,6, 12,12 | AC-2, SC-7 |

Skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Använd hög säkra användar arbets stationer och/eller Azure-skydds för administrativa uppgifter:
- Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och/eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer, begränsad logisk och nätverks åtkomst. 

- Använd Azure skydds-funktionen för en säker sökväg för att få åtkomst till dina virtuella datorer via RDP eller SSH. Azure skydds är en fullständigt hanterad PaaS-tjänst som kan tillhandahållas per virtuellt nätverk utan att skapa ett nytt virtuellt nätverk. 

- [Förstå säkra, Azure-hanterade arbets stationer](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuera en säker, Azure-hanterad arbets Station](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Använd Azure skydds-värd](../../bastion/bastion-create-host-portal.md)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Säkerhetsåtgärdscenter](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identitet och nycklar](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: tilldela behörigheter till resurser med hjälp av Azure RBAC

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-6 | 14,6 | AC-2, AC-3 |

Med rollbaserad åtkomst kontroll i Azure (RBAC) kan du hantera behörigheter till Azure Resource Access via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. 

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md)

- [Konfigurera RBAC i Azure](../../role-based-access-control/role-assignments-portal.md)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetsefterlevnad](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Identitet och nycklar](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: Välj godkännande process för Microsoft Support

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

I support scenarier där Microsoft behöver åtkomst till kund information, Customer Lockbox ger en möjlighet att uttryckligen granska och godkänna eller avvisa varje begäran om kund data åtkomst.

- [Förstå Customer Lockbox](../fundamentals/customer-lockbox-overview.md)

**Ansvar**: kund

**Kund säkerhets intressenter**:

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hantering av säkerhetsefterlevnad](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Identitet och nycklar](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

