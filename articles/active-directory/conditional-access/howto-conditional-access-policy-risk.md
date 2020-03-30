---
title: Villkorlig åtkomst - Riskbaserad villkorlig åtkomst - Azure Active Directory
description: Skapa principer för villkorlig åtkomst för att aktivera förbättringar av identitetsskydd för principer
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295144"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Villkorlig åtkomst: Riskbaserad villkorlig åtkomst

Organisationer med Azure AD Premium P2-licenser kan skapa principer för villkorlig åtkomst som innehåller riskidentifiering av Azure AD Identity Protection. Det finns tre standardprinciper som kan aktiveras direkt. 

* Kräv att alla användare registrerar sig för Azure Multi-Factor Authentication.
* Kräv en lösenordsändring för användare som är högrisk.
* Kräv multifaktorautentisering för användare med medelhög eller hög inloggningsrisk.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Kräv att alla användare registrerar sig för Azure Multi-Factor Authentication

Om du aktiverar den här principen måste alla användare registrera sig för Azure Multi-Factor-autentisering inom 14 dagar. 

1. Logga in på **Azure-portalen**.
1. Klicka på **Alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Klicka på **MFA-registrering**.
1. Under **Tilldelningar**väljer du **Användare**.
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Under **Uteslut**, välj **Välj uteslutna användare,** välj organisationens nödåtkomst eller glasförsträpande konton och välj **Välj**. 
   1. Välj **Done** (Klar).
1. Ange **tvinga princip** till **På**.
1. Klicka på **Spara**.

## <a name="require-a-password-change-high-risk-users"></a>Kräv en lösenordsändring högriskanvändare

Microsoft samarbetar med forskare, polis och rättsväsende, flera säkerhetsteam på Microsoft och andra betrodda källor för att hitta par av användarnamn och lösenord. När något av dessa par matchar ett konto i din miljö kan en riskbaserad lösenordsändring utlösas med hjälp av följande princip.

1. Logga in på **Azure-portalen**.
1. Klicka på **Alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Klicka på **Användarriskpolicy**.
1. Under **Tilldelningar**väljer du **Användare**
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Under **Uteslut**, välj **Välj uteslutna användare,** välj organisationens nödåtkomst eller glasförsträpande konton och välj **Välj**.
   1. Välj **Done** (Klar).
1. Under **Villkor**väljer du **Användarrisk**och väljer sedan **Hög**.
   1. Klicka på **Välj** sedan **Klar**.
1. Under **Kontrollåtkomst** > **Access**väljer du **Tillåt åtkomst**och väljer sedan **Kräv lösenordsändring**.
   1. Klicka på **Markera**.
1. Ange **tvinga princip** till **På**.
1. Klicka på **Spara**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Kräv MFA-medelhög eller hög inloggningsriskanvändare

De flesta användare har ett normalt beteende som kan spåras. När de hamnar utanför den här normen kan det vara riskabelt att tillåta att de bara loggar in. Du kanske vill blockera den användaren eller kanske bara be dem att utföra multifaktorautentisering för att bevisa att de verkligen är vem de säger att de är. Om du vill aktivera en princip som kräver MFA när en riskfylld inloggning har identifierats aktiverar du följande princip.

1. Logga in på **Azure-portalen**.
1. Klicka på **Alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Klicka på **Riskpolicy för inloggning**
1. Under **Tilldelningar**väljer du **Användare**
   1. Under **Inkludera**väljer du **Alla användare**.
   1. Under **Uteslut**, välj **Välj uteslutna användare,** välj organisationens nödåtkomst eller glasförsträpande konton och välj **Välj**.
   1. Välj **Done** (Klar).
1. Under **Villkor**väljer du **Inloggningsrisk**och väljer sedan **Medel och högre**.
   1. Klicka på **Välj** sedan **Klar**.
1. Under **Kontrollåtkomst** > **Access**väljer du **Tillåt åtkomst**och väljer sedan **Kräv multifaktorautentisering**.
   1. Klicka på **Markera**.
1. Ange **tvinga princip** till **På**.
1. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggningsbeteende med verktyget Villkorlig åtkomst Vad händer om](troubleshoot-conditional-access-what-if.md)

[Så här fungerar Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Vad är Azure Active Directory Identity Protection?](../identity-protection/overview.md)
