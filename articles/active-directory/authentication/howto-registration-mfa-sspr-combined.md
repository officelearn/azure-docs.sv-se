---
title: Kom igång med kombinerade registreringen för Azure AD SSPR och MFA (förhandsversion)
description: Aktivera kombineras Azure AD-Multifaktorautentisering och lösenordsåterställning via självbetjäning registrering (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9623cf727998348c313b7184c29e968afd33c6cc
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214154"
---
# <a name="enable-combined-security-information-registration-preview"></a>Aktivera kombineras security information registrering (förhandsversion)

Innan du aktiverar den nya upplevelsen, granska artikeln [kombineras security information registrering (förhandsversion)](concept-registration-mfa-sspr-combined.md) så att du förstår funktioner och effekten av den här funktionen.

![Registrering av förbättrad upplevelse med kombinerade security information begär mer information vid inloggning. Exemplet visar registrera Microsoft Authenticator-appen som första metoden.](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Kombinerade security information registreringen för Azure Multi-Factor Authentication och Azure AD lösenordsåterställning via självbetjäning är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>Aktivera kombinerad registrering

Utför följande steg för att aktivera registreringen av kombinerade:

1. Logga in på Azure-portalen som global administratör eller Användaradministratör.
2. Bläddra till **Azure Active Directory** > **användarinställningar** > **hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.
3. Under **användare kan använda förhandsversionsfunktioner för att registrera och hantera säkerhetsinformation – uppdatera**, väljer att aktivera en **valda** grupp av användare eller **alla** användare.

![Aktivera kombinerade security info förhandsversion upplevelsen för alla användare i Azure AD-portalen](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Från och med mars 2019 telefonsamtal alternativen kommer inte att MFA och SSPR-användare i kostnadsfria/utvärderingsversion Azure AD-klienter. SMS-meddelanden som inte påverkas av den här ändringen. Telefonsamtal fortsätter att vara tillgängliga för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnadsfria/utvärderingsversion Azure AD-klienter.

> [!NOTE]
> När du aktiverar kombinerade registrerings användare registrera eller bekräfta sina telefonnummer eller mobilappar via den nya upplevelsen kan använda dem för MFA och SSPR, om dessa metoder är aktiverade i MFA och SSPR-principer. Om du inaktiverar sedan den här upplevelsen kan användare gå till föregående SSPR-registrering sidan på [https:/aka.ms/ssprsetup](https:/aka.ms/ssprsetup) kommer att behöva utföra multifaktorautentisering innan de kan komma åt sidan.

## <a name="next-steps"></a>Nästa steg

[Tillgängliga metoder för MFA och SSPR](concept-authentication-methods.md)

[Konfigurera lösenordsåterställning via självbetjäning](howto-sspr-deployment.md)

[Konfigurera Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Felsöka kombineras security info registrering](howto-registration-mfa-sspr-combined-troubleshoot.md)