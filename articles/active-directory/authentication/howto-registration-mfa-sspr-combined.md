---
title: Kom igång med kombinerad registrering - Azure Active Directory
description: Aktivera kombinerad azure AD multifaktorautentisering och registrering av återställning av lösenordsåterbeställning med självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639685"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Aktivera kombinerad registrering av säkerhetsinformation i Azure Active Directory

Före kombinerad registrering registrerade användare autentiseringsmetoder för Azure Multi-Factor Authentication och self-service password reset (SSPR) separat. Människor var förvirrade över att liknande metoder användes för Multi-Factor Authentication och SSPR men de var tvungna att registrera sig för båda funktionerna. Nu, med kombinerad registrering, användare kan registrera sig en gång och få fördelarna med både Multi-Factor Authentication och SSPR.

Innan du aktiverar den nya upplevelsen bör du läsa artikeln [Kombinerad registrering](concept-registration-mfa-sspr-combined.md) av säkerhetsinformation för att se till att du förstår funktionens funktioner och effekter.

![Kombinerad förbättrad upplevelse av ökad upplevelse av säkerhetsinformation](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Aktivera kombinerad registrering

Gör så här för att aktivera kombinerad registrering:

1. Logga in på Azure-portalen som användaradministratör eller global administratör.
2. Gå till Azure Active > **Directory-användarinställningar** > **Hantera förhandsgranskningsinställningar för användarfunktionen**. **Azure Active Directory**
3. Under **Användare kan använda förhandsgranskningsfunktioner för att registrera och hantera säkerhetsinformation**väljer du att aktivera för en **vald** grupp användare eller för **alla** användare.

   ![Aktivera den kombinerade förhandsgranskningsupplevelsen för säkerhetsinformation för alla användare](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> När du har aktiverat kombinerad registrering kan användare som registrerar eller bekräftar sitt telefonnummer eller mobilapp via den nya upplevelsen använda dem för multifaktorautentisering och SSPR, om dessa metoder är aktiverade i principerna för multifaktorautentisering och SSPR. Om du sedan inaktiverar den här upplevelsen måste användare `https://aka.ms/ssprsetup` som går till den tidigare SSPR-registreringssidan på utföra multifaktorautentisering innan de kan komma åt sidan.

Om du har konfigurerat listan Webbplats till zontilldelning i Internet Explorer måste följande platser finnas i samma zon:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Principer för villkorlig åtkomst för kombinerad registrering

Det är nu möjligt att skydda när och hur användare registrerar sig för Azure Multi-Factor Authentication och självbetjäningslösenordsåterställning med användaråtgärder i principen villkorlig åtkomst. Den här funktionen är tillgänglig för organisationer som har aktiverat den [kombinerade registreringsfunktionen](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan aktiveras i organisationer där de vill att användare ska registrera sig för Azure Multi-Factor Authentication och SSPR från en central plats, till exempel en betrodd nätverksplats under hr-introduktion.

Mer information om hur du skapar betrodda platser i Villkorlig åtkomst finns i artikeln [Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip som kräver registrering från en betrodd plats

Följande princip gäller för alla valda användare som försöker registrera sig med den kombinerade registreringsupplevelsen och blockerar åtkomst om de inte ansluter från en plats som är markerad som ett betrott nätverk.

1. I **Azure-portalen**bläddrar du till **Azure Active Directory** > **Security** > Conditional**Access**
1. Välj **+ Ny princip**
1. Ange ett namn för den här principen, till exempel *Registrering av kombinerad säkerhetsinformation i betrodda nätverk*.
1. Under **Tilldelningar** väljer du **Användare och grupper**. Välj de användare och grupper som du vill att den här principen ska gälla för och välj sedan **Klar**.

   > [!WARNING]
   > Användare måste vara aktiverade för kombinerad registrering.

1. Under **Molnappar eller -åtgärder**väljer du **Användaråtgärder**. Kontrollera **Registrera säkerhetsinformation**och välj sedan **Klar**.

    ![Skapa en princip för villkorlig åtkomst för att kontrollera registrering av säkerhetsinformation](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Konfigurera följande alternativ under > **Villkorsplatser:** **Conditions**
   1. Konfigurera **Ja**
   1. Inkludera **valfri plats**
   1. Exkludera **alla betrodda platser**
1. Välj **Klar** i fönstret *Platser* och välj sedan **Klar** i fönstret *Villkor.*
1. Under Bevilja**Tilldelning** **av** > Åtkomstkontroller väljer du **Blockera åtkomst**och välj sedan **Välj**
1. Ange **Aktivera princip** till **På**
1. Om du vill slutföra principen väljer du **Skapa**

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp kan du läsa fel [felsöka kombinerad registrering av säkerhetsinformation](howto-registration-mfa-sspr-combined-troubleshoot.md) eller läsa [Vilket platsvillkor i Azure Active Directory Villkorlig åtkomst?](../conditional-access/location-condition.md)

Om du vill aktivera funktionerna i din Azure AD-klient finns i självstudierna för att [aktivera återställning av lösenord för självbetjäning](tutorial-enable-sspr.md) och aktivera Azure [Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Lär dig hur du [aktiverar kombinerad registrering i din klientorganisation](howto-registration-mfa-sspr-combined.md) eller [tvingar användare att registrera om autentiseringsmetoder](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Du kan också granska [tillgängliga metoder för Azure Multi-Factor Authentication och SSPR](concept-authentication-methods.md).
