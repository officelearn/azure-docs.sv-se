---
title: Villkorlig åtkomst – riskfylld-baserad villkorlig åtkomst-Azure Active Directory
description: Skapa principer för villkorlig åtkomst för att aktivera identitets skydds förbättringar för principer
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9cfba377aba30d4687bab4ba7c5a311c70c4905
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995164"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Villkorlig åtkomst: riskfylld villkorlig åtkomst

Organisationer med Azure AD Premium P2-licenser kan skapa principer för villkorlig åtkomst som innehåller Azure AD Identity Protection risk identifieringar. Det finns tre standard principer som kan aktive ras från rutan. 

* Kräv att alla användare registrerar sig för Azure Multi-Factor Authentication.
* Kräv en lösen ords ändring för användare med hög risk.
* Kräv Multi-Factor Authentication för användare med medelhög eller hög inloggnings risk.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Kräv att alla användare registrerar sig för Azure Multi-Factor Authentication

Att aktivera den här principen kräver att alla användare registrerar sig för Azure Multi-Factor Authentication inom 14 dagar. 

1. Logga in på **Azure-portalen**.
1. Klicka på **Alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Klicka på **MFA-registrering**.
1. Under **tilldelningar**väljer **du användare**.
   1. Under **Inkludera**väljer du **alla användare**.
   1. Under **exkludera**väljer du **Välj exkluderade användare**, väljer din organisations nödfalls åtkomst eller rast glass konton och väljer **Välj**. 
   1. Välj **Done** (Klar).
1. Ange **tillämpa principen** på **på**.
1. Klicka på **Spara**.

## <a name="require-a-password-change-high-risk-users"></a>Kräv lösen ord för användare med hög risk

Microsoft samarbetar med forskare, polis och rättsväsende, flera säkerhetsteam på Microsoft och andra betrodda källor för att hitta par av användarnamn och lösenord. När något av dessa par matchar ett konto i din miljö kan en riskbaserad lösenordsändring utlösas med hjälp av följande princip.

1. Logga in på **Azure-portalen**.
1. Klicka på **Alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Klicka på **princip för användar risk**.
1. Under **tilldelningar**väljer **du användare**
   1. Under **Inkludera**väljer du **alla användare**.
   1. Under **exkludera**väljer du **Välj exkluderade användare**, väljer din organisations nödfalls åtkomst eller rast glass konton och väljer **Välj**.
   1. Välj **Done** (Klar).
1. Välj **användar risk**under **villkor**och välj sedan **hög**.
   1. Klicka på **Välj** och sedan på **Slutför**.
1. Under **kontroller**  >  **åtkomst**väljer du **Tillåt åtkomst**och väljer sedan **Kräv lösen ords ändring**.
   1. Klicka på **Välj**.
1. Ange **tillämpa principen** på **på**.
1. Klicka på **Spara**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Kräv användare av MFA-medium eller hög inloggnings risk

De flesta användare har ett normalt beteende som kan spåras. När de hamnar utanför den här normen kan det vara riskabelt att tillåta att de bara loggar in. Du kanske vill blockera användaren eller bara be dem att utföra Multi-Factor Authentication för att bevisa att de verkligen är de som de säger att de är. Om du vill aktivera en princip som kräver MFA när en riskfylld inloggning har identifierats aktiverar du följande princip.

1. Logga in på **Azure-portalen**.
1. Klicka på **Alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Klicka på **princip för inloggnings risk**
1. Under **tilldelningar**väljer **du användare**
   1. Under **Inkludera**väljer du **alla användare**.
   1. Under **exkludera**väljer du **Välj exkluderade användare**, väljer din organisations nödfalls åtkomst eller rast glass konton och väljer **Välj**.
   1. Välj **Done** (Klar).
1. Under **villkor**väljer du **inloggnings risk**och väljer sedan **medel och över**.
   1. Klicka på **Välj** och sedan på **Slutför**.
1. Under **kontroller**  >  **åtkomst**väljer du **Tillåt åtkomst**och väljer sedan **Kräv Multi-Factor Authentication**.
   1. Klicka på **Välj**.
1. Ange **tillämpa principen** på **på**.
1. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-report-only.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)

[Så här fungerar Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Vad är Azure Active Directory Identity Protection?](../identity-protection/overview.md)
