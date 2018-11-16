---
title: Azure AD-distributionschecklista 30 dagar, 90 dagar, och även efteråt
description: Azure Active Directory Premium P2 checklista för distribution av funktionen
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: ''
ms.openlocfilehash: 86561cd835a36282ca1b38638ab4372c6b360617
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705659"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Azure Active Directory Premium P2 licensiering funktionen Checklista

Det kan verka överväldigande att distribuera Azure Active Directory (Azure AD) för din organisation och skydda den. Den här artikeln beskrivs några vanliga saker som kunderna användbara. Kunder vanligtvis utföra dessa uppgifter under 30 dagar, 90 dagar, eller efter för att förbättra sin säkerhetsposition. Även organisationer som redan har distribuerat Azure AD kan använda den här checklistan för att se till att de får ut maximalt av sina investeringar.

Ett framgångsrikt och utförs identitetsinfrastrukturen banar vägen för säkrare åtkomst till din produktivitet arbetsbelastningar och data genom att endast autentiserade användare och enheter.

## <a name="prerequisites"></a>Förutsättningar

Den här handboken förutsätts att du har licenser för Azure AD Premium P2, Enterprise Mobility + Security E5, Microsoft 365 E5 eller ett liknande licens-paket.

[Azure AD-licensiering](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>Planera och distribuera: dag 1 – 30

- Ange mer än en global administratör (glas konto)
   - [Hantera nödfall-åtkomst till administratörskonton i Azure AD](../users-groups-roles/directory-emergency-access.md)
- Aktivera Azure AD Privileged Identity Management (PIM) för att visa rapporter
   - [Börja använda PIM](../privileged-identity-management/pim-getting-started.md)
- Använda icke-globala administrativa roller där det är möjligt.
   - [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- Autentisering
   - [Lansera självåterställning av lösenord](../authentication/howto-sspr-deployment.md)
   - Distribuera Azure AD-lösenord Protection (förhandsversion)
      - [Eliminera felaktiga lösenord i din organisation](../authentication/concept-password-ban-bad.md)
      - [Använda Azure AD-lösenordsskydd för Windows Server Active Directory](../authentication/concept-password-ban-bad-on-premises.md)
   - Konfigurera principer för kontoutelåsning
      - [Azure Active Directory smart kontoutelåsning](../authentication/howto-password-smart-lockout.md)
      - [AD FS Extranätsutelåsning och Smart Extranätsutelåsning](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [Distribuera Azure AD Multi-Factor Authentication med hjälp av principer för villkorlig åtkomst](../authentication/howto-mfa-getstarted.md)
   - [Aktivera konvergerade registrering för lösenordsåterställning via självbetjäning och Azure AD Multi-Factor Authentication (förhandsversion)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Aktivera Azure Active Directory Identity Protection](../identity-protection/enable.md)
      - [Använda riskhändelser utlösaren Multi-Factor Authentication och ändring av lösenord](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [Riktlinjer för lösenord](https://www.microsoft.com/research/publication/password-guidance/)
      - Underhålla en åtta tecken minimilängden, längre är inte nödvändigtvis bättre.
      - Eliminera krav för tecken-sammansättning.
      - [Eliminera obligatoriska periodiska lösenordsåterställning för användarkonton.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- Synkronisera användare från en lokal Active Directory
   - [Installera Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [Implementera lösenordets Hash-synkronisering](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [Implementera tillbakaskrivning av lösenord](../authentication/howto-sspr-writeback.md)
   - [Implementera Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [Tilldela licenser till användare av gruppmedlemskap i Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>Planera och distribuera: 31 90 dagar

- [Planera för gästanvändares åtkomst](../b2b/what-is-b2b.md)
   - [Lägg till användare i Azure Active Directory B2B-samarbetet i Azure portal](../b2b/add-users-administrator.md)
   - [Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer](../b2b/allow-deny-list.md)
   - [Grant B2B-användare i Azure AD åtkomst till dina lokala program](../b2b/hybrid-cloud-to-on-premises.md)
- Fatta beslut om användaren strategi för hantering av livscykeln
- [Besluta om enhetshanteringsstrategi](../devices/overview.md)
   - [Användningsscenarier och överväganden vid distribution för Azure AD Join](../devices/azureadjoin-plan.md)
- [Hantera Windows Hello för företag i din organisation](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>Planera och distribuera: 90 dagar och senare

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [Konfigurera Azure AD directory rollinställningar i PIM](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [Tilldela Azure AD-katalogroller i PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [Slutför en åtkomstgranskning för Azure AD-katalogroller i PIM](../privileged-identity-management/pim-how-to-start-security-review.md)
- Hantera livscykeln för användaren holistiskt
   - Azure AD har en metod för att hantera identitetslivscykel
   - Ta bort manuella steg från din anställdas konto livscykel, för att förhindra obehörig åtkomst:
      - Synkronisera identiteter från din källa till sanningen (HR-systemet) till Azure AD. länka till HR-system som stöds)
      - [Använda dynamiska grupper för att automatiskt tilldela användare i grupper baserat på deras attribut från HR (eller din källa till sanningen), till exempel avdelning, rubrik, region, och andra attribut.](../users-groups-roles/groups-dynamic-membership.md)
      - [Använd gruppbaserad åtkomsthantering driftsättning till automatiskt etablera användare för SaaS-program.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>Nästa steg

[Konfigurationer för identitets- och Enhetsåtkomst](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Gemensamma rekommenderade principer för åtkomst av identitets- och Enhetsåtkomst](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
