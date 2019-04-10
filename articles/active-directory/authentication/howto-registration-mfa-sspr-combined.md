---
title: Kom igång med kombinerade registreringen för Azure AD SSPR och Multi-Factor Authentication (förhandsversion) – Azure Active Directory
description: Aktivera kombineras Azure AD-Multifaktorautentisering och lösenordsåterställning via självbetjäning registrering (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280578"
---
# <a name="enable-combined-security-information-registration-preview"></a>Aktivera kombineras security information registrering (förhandsversion)

Innan du aktiverar den nya upplevelsen, granska artikeln [kombineras security information registrering (förhandsversion)](concept-registration-mfa-sspr-combined.md) så att du förstår funktioner och effekterna av den här funktionen.

![Kombinerade security information registrering förbättrad upplevelse](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Kombinerade security information registreringen för Azure Multi-Factor Authentication och återställning av lösenord för Azure Active Directory (Azure AD) är en funktion i offentliga förhandsversionen av Azure AD. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Aktivera kombinerad registrering

Utför stegen nedan för att aktivera registreringen av kombinerade:

1. Logga in på Azure-portalen som global administratör eller Användaradministratör.
2. Gå till **Azure Active Directory** > **användarinställningar** > **hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.
3. Under **användare kan använda förhandsversionsfunktioner för att registrera och hantera säkerhetsinformation – uppdatera**, väljer att aktivera en **valda** grupp av användare eller **alla** användare.

   ![Aktivera kombinerade security info förhandsversion upplevelsen för alla användare](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> Från och med mars 2019, är telefonsamtal alternativ inte tillgängliga för multi-Factor Authentication och SSPR-användare i kostnadsfria/utvärderingsversion Azure AD-klienter. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtal-alternativ kan fortfarande tillgängliga för användare i betalda Azure AD-klienter.

> [!NOTE]
> När du har aktiverat kombineras registreringen användare registrera eller bekräfta sina telefonnummer eller mobilappar via den nya upplevelsen kan använda dem för Multifaktorautentisering och SSPR, om dessa metoder är aktiverade i Multi-Factor Authentication och SSPR principer. Om du inaktiverar sedan den här upplevelsen kan användare gå till föregående SSPR-registrering sidan på `https:/aka.ms/ssprsetup` kommer att behöva utföra multifaktorautentisering innan de kan komma åt sidan.

Om du har konfigurerat platser till zoner tilldelning i Internet Explorer, måste på följande webbplatser vara i samma zon:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Nästa steg

[Tillgängliga metoder för Multifaktorautentisering och SSPR](concept-authentication-methods.md)

[Konfigurera lösenordsåterställning via självbetjäning](howto-sspr-deployment.md)

[Konfigurera Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Felsöka kombineras security info registrering](howto-registration-mfa-sspr-combined-troubleshoot.md)