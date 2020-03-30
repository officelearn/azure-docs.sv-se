---
title: Konfigurera MFA-registreringsprincipen - Azure Active Directory Identity Protection
description: Lär dig hur du konfigurerar azure AD Identity Protection multifaktorautentiseringsregistreringsprincipen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382134"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Så här konfigurerar du registreringsprincipen för multifaktorautentisering i Azure

Azure AD Identity Protection hjälper dig att hantera utrullningen av MFA-registrering (Azure Multi-Factor Authentication) genom att konfigurera en princip för villkorlig åtkomst för att kräva MFA-registrering oavsett vilken modern autentiseringsapp du loggar in på.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Vad är registreringsprincipen för Azure Multi-Factor Authentication?

Azure Multi-Factor Authentication är ett sätt att verifiera vem du använder mer än bara ett användarnamn och lösenord. Det ger ett andra lager av säkerhet för användarens inloggningar. För att användare ska kunna svara på MFA-uppmaningar måste de först registrera sig för Azure Multi-Factor Authentication.

Vi rekommenderar att du kräver Azure Multi-Factor Autentisering för användare inloggningar eftersom det:

- Ger stark autentisering genom en rad verifieringsalternativ.
- Spelar en nyckelroll i att förbereda din organisation för att själv åtgärda från riskidentifieringar i Identity Protection.

Mer information om Azure Multi-Factor Authentication finns i [Vad är Azure Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Principkonfiguration

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** > **Security** > **Identity Protection** > **MFA-registreringsprincip**.
   1. Under **Uppdrag**
      1. **Användare** – Välj **Alla användare** eller Välj personer **och grupper** om du begränsar distributionen.
         1. Du kan också välja att utesluta användare från principen.
   1. Under **Kontroller**
      1. Kontrollera kryssrutan **Kräv att Azure MFA-registrering** är markerad och välj **Välj**.
   1. **Framtvinga princip** - **på**
   1. **Spara**

## <a name="user-experience"></a>Användarupplevelse

Azure Active Directory Identity Protection uppmanar användarna att registrera sig nästa gång de loggar in interaktivt och de har 14 dagar på sig att slutföra registreringen. Under denna 14-dagarsperiod kan de kringgå registreringen, men i slutet av perioden måste de registrera sig innan de kan slutföra inloggningsprocessen.

En översikt över den relaterade användarupplevelsen finns i:

- [Inloggningsupplevelser med Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Nästa steg

- [Aktivera principer för inloggning och användarrisk](howto-identity-protection-configure-risk-policies.md)

- [Aktivera azure ad-återställning av lösenord med självbetjäning](../authentication/howto-sspr-deployment.md)

- [Aktivera Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
