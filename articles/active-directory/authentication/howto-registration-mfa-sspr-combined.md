---
title: Kom igång med kombinerade registreringen för Azure AD SSPR och Multi-Factor Authentication (förhandsversion) – Azure Active Directory
description: Aktivera kombineras Azure AD-Multifaktorautentisering och lösenordsåterställning via självbetjäning registrering (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/1/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: abe9cba604100a42a4cd29ccd5af47e8898ea409
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812938"
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

## <a name="conditional-access-policies-for-combined-registration"></a>Principer för villkorlig åtkomst för kombinerade registrering

Säkra när och hur användare registrera dig för Azure Multi-Factor Authentication och lösenordsåterställning via självbetjäning är nu möjligt med användaråtgärder i principen för villkorlig åtkomst. Den här funktionen för förhandsgranskning är tillgänglig för organisationer som har aktiverat den [kombineras registrering förhandsversion](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan aktiveras i organisationer där de vill att användare registrerar sig för Azure Multi-Factor Authentication och SSPR från en central plats, till exempel en betrodd nätverksplats under HR-integrering. Mer information om hur du skapar betrodda platser i villkorlig åtkomst finns i artikeln [vad är platsvillkoret för villkorlig åtkomst i Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip för att kräva registrering från en betrodd plats

Följande principen gäller för alla markerade användare som försöker registrera dig med den kombinerade registrerings-upplevelse och blockerar åtkomsten om de ansluter från en plats som markerats som betrott nätverk.

![Skapa en princip för CA: N för att styra security info registrering](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. I den **Azure-portalen**, bläddra till **Azure Active Directory** > **villkorlig åtkomst**
1. Välj **Ny princip**
1. Ange ett namn för den här principen i namn. Till exempel **kombineras Security Info registreringen på betrodda nätverk**
1. Under **tilldelningar**, klickar du på **användare och grupper**, och välj de användare och grupper som du vill att den här principen ska tillämpas på

   > [!WARNING]
   > Användare måste aktiveras för den [kombineras registrering förhandsversion](../authentication/howto-registration-mfa-sspr-combined.md).

1. Under **molnbaserade appar eller åtgärder**väljer **användaråtgärder**, kontrollera **registrera säkerhetsinformation (förhandsversion)**
1. Under **villkor** > **platser**
   1. Konfigurera **Ja**
   1. Inkludera **valfri plats**
   1. Exkludera **alla betrodda platser**
   1. Klicka på **klar** på bladet platser
   1. Klicka på **klar** på bladet villkor
1. Under **åtkomstkontroller** > **bevilja**
   1. Klicka på **blockera åtkomst**
   1. Klicka sedan på **Välj**
1. Ange **aktiverar principen** till **på**
1. Klicka sedan på **skapa**

## <a name="next-steps"></a>Nästa steg

[Tillgängliga metoder för Multifaktorautentisering och SSPR](concept-authentication-methods.md)

[Konfigurera lösenordsåterställning via självbetjäning](howto-sspr-deployment.md)

[Konfigurera Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Felsöka kombineras security info registrering](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Vad är platsvillkoret för villkorlig åtkomst i Azure Active Directory?](../conditional-access/location-condition.md)