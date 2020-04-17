---
title: Kom igång med kombinerad registrering - Azure Active Directory
description: Aktivera kombinerad azure AD multifaktorautentisering och registrering av återställning av lösenordsåterbeställning med självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d9544b1f4dd5ecbf66493f26c373c5502dce68a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451097"
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

Det är nu möjligt att skydda när och hur användare registrerar sig för Azure Multi-Factor Authentication och självbetjäningslösenordsåterställning med användaråtgärder i principen villkorlig åtkomst. Den här funktionen är tillgänglig för organisationer som har aktiverat den [kombinerade registreringsfunktionen](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan aktiveras i organisationer där de vill att användare ska registrera sig för Azure Multi-Factor Authentication och SSPR från en central plats, till exempel en betrodd nätverksplats under hr-introduktion. Mer information om hur du skapar betrodda platser i Villkorlig åtkomst finns i artikeln [Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip som kräver registrering från en betrodd plats

Följande princip gäller för alla valda användare som försöker registrera sig med den kombinerade registreringsupplevelsen och blockerar åtkomst om de inte ansluter från en plats som markerats som ett betrott nätverk.

![Skapa en certifikatutfärdarprincip för att styra registreringen av säkerhetsinformation](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. I **Azure-portalen**bläddrar du till **Azure Active Directory** > **Security** > Conditional**Access**
1. Välj **Ny princip**
1. Ange ett namn för den här principen i Namn. **Kombinerad registrering av säkerhetsinformation i betrodda nätverk**
1. Under **Tilldelningar**klickar du på **Användare och grupper**och väljer de användare och grupper som du vill att den här principen ska gälla för

   > [!WARNING]
   > Användare måste vara aktiverade för [kombinerad registrering](../authentication/howto-registration-mfa-sspr-combined.md).

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

Om du behöver hjälp kan du läsa fel [felsöka kombinerad registrering av säkerhetsinformation](howto-registration-mfa-sspr-combined-troubleshoot.md) eller läsa [Vilket platsvillkor i Azure Active Directory Villkorlig åtkomst?](../conditional-access/location-condition.md)

Om du vill aktivera funktionerna i din Azure AD-klient finns i självstudierna för att [aktivera återställning av lösenord för självbetjäning](tutorial-enable-sspr.md) och aktivera Azure [Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Lär dig hur du [aktiverar kombinerad registrering i din klientorganisation](howto-registration-mfa-sspr-combined.md) eller [tvingar användare att registrera om autentiseringsmetoder](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Du kan också granska [tillgängliga metoder för Azure Multi-Factor Authentication och SSPR](concept-authentication-methods.md).
