---
title: Så här konfigurerar du principen för Multi-Factor Authentication-registrering i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar registrerings principen för Azure AD Identity Protection Multi-Factor Authentication.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939f08fd16cf27e641cf6436a00396ad2db8e6c3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126395"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Instruktioner: Konfigurera registrerings principen för Azure-Multi-Factor Authentication

Azure AD Identity Protection hjälper dig att hantera distributionen av Multi-Factor Authentication-registrering (MFA) genom att konfigurera en princip för villkorlig åtkomst för att kräva MFA-registrering oavsett vilken modern app för autentisering som du loggar in på. Den här artikeln förklarar vad principen kan användas för och hur du konfigurerar den.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Vad är registrerings principen för Azure-Multi-Factor Authentication?

Med Azure Multi-Factor Authentication kan du kontrol lera vem du använder mer än bara ett användar namn och lösen ord. Det innehåller ett andra säkerhets lager för användar inloggningar. För att användarna ska kunna svara på MFA-prompter måste de först registrera sig för Azure Multi-Factor Authentication.

Vi rekommenderar att du kräver Azure Multi-Factor Authentication för användar inloggningar eftersom det:

- Ger stark autentisering med en rad enkla verifierings alternativ
- Spelar en viktig roll i förbereda din organisation för att skydda och återställa från risk identifieringar i identitets skydd

Mer information om MFA finns i [Vad är Azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>Hur gör jag för att åtkomst till registrerings principen?

Principen för MFA-registreringen finns i avsnittet **Konfigurera** på [sidan Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).

![MFA-princip](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>Principinställningar

När du konfigurerar MFA-registrerings principen måste du göra följande ändringar i konfigurationen:

- De användare och grupper som principen gäller för. Kom ihåg att undanta din organisations [konto för nöd åtkomst](../users-groups-roles/directory-emergency-access.md).

    ![Användare och grupper](./media/howto-mfa-policy/11.png)

- Den kontroll som du vill genomdriva – **kräver Azure MFA-registrering**

    ![Åtkomst](./media/howto-mfa-policy/12.png)

- Tvinga fram princip ska vara inställt **på on**.

    ![Tillämpa princip](./media/howto-mfa-policy/14.png)

- **Spara** principen

## <a name="user-experience"></a>Användarupplevelse

Azure Active Directory Identity Protection uppmanas användarna att registrera sig nästa gången de loggar in interaktivt.

En översikt över den relaterade användar upplevelsen finns i:

- [Registrerings flöde för Multi-Factor Authentication](flows.md#multi-factor-authentication-registration).  
- [Inloggnings upplevelser med Azure AD Identity Protection](flows.md).  

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection, se [Översikt över Azure AD Identity Protection](overview.md).
