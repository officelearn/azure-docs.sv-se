---
title: Så här konfigurerar du registreringsprincip för multi-Factor authentication i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar registreringsprincip för multifaktorautentisering i Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f4083ddf849842358f7699badca6598e56e4dee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65139369"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Instruktioner: Konfigurera Azure Multi-Factor Authentication-registreringsprincip

Azure AD Identity Protection hjälper dig att hantera lansering av registrering för multifaktorautentisering (MFA) genom att konfigurera en princip för villkorlig åtkomst för att kräva MFA-registrering oavsett vilken app du loggar in på. Den här artikeln förklarar vad principen kan användas för och hur du konfigurerar den.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Vad är Azure Multi-Factor Authentication-registreringsprincip?

Azure Multi-Factor Authentication ger ett sätt att verifiera vem du använder mer än bara ett användarnamn och lösenord. Det ger ett andra säkerhetslager till användarinloggningar. För att användare ska kunna svara på förfrågningar för MFA, måste de först registrera för Azure Multi-Factor Authentication.

Vi rekommenderar att du kräver Azure Multi-Factor Authentication för användarinloggningar eftersom den:

- Du får stark autentisering med en rad enkla verifieringsalternativ
- Spelar en viktig roll i att förbereda din organisation för att skydda och återställa från riskhändelser på Identity Protection

Mer information om MFA finns i [vad är Azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Hur kommer jag åt registrering?

MFA-registreringsprincip finns i den **konfigurera** avsnittet på den [Azure AD Identity Protection-sidan](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![MFA-principen](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Principinställningar

När du konfigurerar MFA-registreringsprincip, måste du göra följande ändringar i konfigurationen:

- Användare och grupper som principen gäller för. Kom ihåg att undanta organisationens [för åtkomst vid akutfall](../users-groups-roles/directory-emergency-access.md).

    ![Användare och grupper](./media/howto-mfa-policy/11.png)

- Den kontroll som du vill framtvinga - **kräver Azure MFA-registrering**

    ![Access](./media/howto-mfa-policy/12.png)

- Framtvinga princip ska vara inställd på **på**.

    ![Tillämpa princip](./media/howto-mfa-policy/14.png)

- **Spara** din princip

## <a name="user-experience"></a>Användarupplevelse

En översikt över relaterade användarupplevelsen finns:

- [Flöde för registrering av multifaktorautentisering](flows.md#multi-factor-authentication-registration).  
- [Logga in som inträffar med Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview.md).
