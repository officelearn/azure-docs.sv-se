---
title: Kom igång med kombinerad registrering - Azure Active Directory
description: Aktivera kombinerad Azure AD Multi-Factor-autentisering och registrering av återställning av lösenordsåterbeställning med självbetjäning (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25c64d9e959b1d68de23e83e26d3495bd3939986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425162"
---
# <a name="enable-combined-security-information-registration-preview"></a>Aktivera kombinerad registrering av säkerhetsinformation (förhandsgranskning)

Innan du aktiverar den nya upplevelsen bör du läsa artikeln Kombinerad registrering av [säkerhetsinformation (förhandsversion)](concept-registration-mfa-sspr-combined.md) för att se till att du förstår funktionens funktioner och effekter.

![Kombinerad förbättrad upplevelse av ökad upplevelse av säkerhetsinformation](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Kombinerad registrering av säkerhetsinformation för Azure Multi-Factor Authentication och Azure Active Directory (Azure AD) självbetjäningslösenordsåterställning är en offentlig förhandsversion av Azure AD. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Organisationer som har aktiverat den tidigare förhandsversionen för registrering och hantering av säkerhetsinformation bör följa stegen nedan för att aktivera den förbättrade förhandsversionen. För organisationer som inte gör bytet, den 8 oktober 2019, kommer Microsoft att byta användare av den tidigare förhandsversionen för att registrera och hantera säkerhetsinformation till den förbättrade upplevelsen. 
> 
> Om du inte har aktiverat någon version av förhandsgranskningen kommer din organisation inte att påverkas.

## <a name="enable-combined-registration"></a>Aktivera kombinerad registrering

Gör så här för att aktivera kombinerad registrering:

1. Logga in på Azure-portalen som användaradministratör eller global administratör.
2. Gå till Azure Active > **Directory-användarinställningar** > **Hantera förhandsgranskningsinställningar för användarfunktionen**. **Azure Active Directory**
3. Under **Användare kan använda förhandsgranskningsfunktioner för att registrera och hantera säkerhetsinformation**väljer du att aktivera för en **vald** grupp användare eller för **alla** användare.

   ![Aktivera den kombinerade förhandsgranskningsupplevelsen för säkerhetsinformation för alla användare](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> Från och med mars 2019 är samtalsalternativen inte tillgängliga för Multifaktorautentisering och SSPR-användare i Azure AD-klienter med kostnadsfri/utvärderingsversion. SMS-meddelanden påverkas inte av den här ändringen. Alternativen för telefonsamtal är fortfarande tillgängliga för användare i betalda Azure AD-klienter.

> [!NOTE]
> När du har aktiverat kombinerad registrering kan användare som registrerar eller bekräftar sitt telefonnummer eller mobilapp via den nya upplevelsen använda dem för multifaktorautentisering och SSPR, om dessa metoder är aktiverade i multifaktorautentiseringen och SSPR Politik. Om du sedan inaktiverar den här upplevelsen måste användare `https://aka.ms/ssprsetup` som går till den tidigare SSPR-registreringssidan på utföra multifaktorautentisering innan de kan komma åt sidan.

Om du har konfigurerat listan Webbplats till zontilldelning i Internet Explorer måste följande platser finnas i samma zon:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Principer för villkorlig åtkomst för kombinerad registrering

Det är nu möjligt att skydda när och hur användare registrerar sig för Azure Multi-Factor Authentication och självbetjäningslösenordsåterställning med användaråtgärder i principen villkorlig åtkomst. Den här förhandsgranskningsfunktionen är tillgänglig för organisationer som har aktiverat den [kombinerade förhandsversionen av registreringen](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan aktiveras i organisationer där de vill att användare ska registrera sig för Azure Multi-Factor Authentication och SSPR från en central plats, till exempel en betrodd nätverksplats under hr-introduktion. Mer information om hur du skapar betrodda platser i Villkorlig åtkomst finns i artikeln [Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip som kräver registrering från en betrodd plats

Följande princip gäller för alla valda användare som försöker registrera sig med den kombinerade registreringsupplevelsen och blockerar åtkomst om de inte ansluter från en plats som markerats som ett betrott nätverk.

![Skapa en certifikatutfärdarprincip för att styra registreringen av säkerhetsinformation](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. I **Azure-portalen**bläddrar du till **Azure Active Directory** > **Security** > Conditional**Access**
1. Välj **Ny princip**
1. Ange ett namn för den här principen i Namn. **Kombinerad registrering av säkerhetsinformation i betrodda nätverk**
1. Under **Tilldelningar**klickar du på **Användare och grupper**och väljer de användare och grupper som du vill att den här principen ska gälla för

   > [!WARNING]
   > Användare måste vara aktiverade för den [kombinerade registreringsförhandsgranskningen](../authentication/howto-registration-mfa-sspr-combined.md).

1. Under **Molnappar eller -åtgärder**väljer du **Användaråtgärder**, markerar **Registrera säkerhetsinformation (förhandsgranskning)**
1. Under **förhållanden** > **platser**
   1. Konfigurera **Ja**
   1. Inkludera **valfri plats**
   1. Exkludera **alla betrodda platser**
   1. Klicka på **Klar** på platsbladet
   1. Klicka på **Klar** på bladet Villkor
1. Under **Åtkomstkontroller** > **Bevilja**
   1. Klicka på **Blockera åtkomst**
   1. Klicka sedan på **Välj**
1. Ange **Aktivera princip** till **På**
1. Klicka sedan på **Skapa**

## <a name="next-steps"></a>Nästa steg

[Tvinga användare att registrera om autentiseringsmetoder](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Tillgängliga metoder för multifaktorautentisering och SSPR](concept-authentication-methods.md)

[Konfigurera återställning av lösenord för självbetjäning](howto-sspr-deployment.md)

[Konfigurera Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

[Felsöka kombinerad registrering av säkerhetsinformation](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst?](../conditional-access/location-condition.md)
