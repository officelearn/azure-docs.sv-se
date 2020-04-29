---
title: Kom igång med kombinerad registrerings Azure Active Directory
description: Aktivera kombinerat Azure AD-Multi-Factor Authentication och registrering av lösen ords återställning via självbetjäning
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639685"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Aktivera kombinerad säkerhets informations registrering i Azure Active Directory

Innan en kombinerad registrering har användare registrerat autentiseringsmetoder för Azure Multi-Factor Authentication och lösen ords återställning via självbetjäning (SSPR) separat. Personer har förvirrat att liknande metoder användes för Multi-Factor Authentication och SSPR, men de var tvungna att registrera sig för båda funktionerna. Med kombinerad registrering kan användarna registrera sig en gång och få fördelarna med både Multi-Factor Authentication-och SSPR.

Innan du aktiverar den nya upplevelsen kan du läsa artikeln [kombinerad säkerhets informations registrering](concept-registration-mfa-sspr-combined.md) för att se till att du förstår funktionerna och effekterna av den här funktionen.

![Kombinerad säkerhets informations registrering förbättrad upplevelse](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Aktivera kombinerad registrering

Utför de här stegen för att aktivera kombinerad registrering:

1. Logga in på Azure Portal som en användar administratör eller global administratör.
2. Gå till **Azure Active Directory** > **användar inställningar** > **hantera användar funktions förhands gransknings inställningar**.
3. Under **användare kan använda för hands versions funktioner för att registrera och hantera säkerhets information**, välja att aktivera för en **vald** grupp användare eller för **alla** användare.

   ![Aktivera den kombinerade för hands versionen av säkerhets information för alla användare](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> När du har aktiverat kombinerad registrering kan användare som registrerar eller bekräftar sina telefonnummer eller mobilappar via den nya upplevelsen använda dem för Multi-Factor Authentication och SSPR, om dessa metoder är aktiverade i Multi-Factor Authentication-och SSPR-principerna. Om du sedan inaktiverar den här funktionen, krävs det att användare som går till den tidigare `https://aka.ms/ssprsetup` registrerings sidan för SSPR måste utföra Multi-Factor Authentication innan de kan komma åt sidan.

Om du har konfigurerat plats till zon tilldelnings lista i Internet Explorer måste följande platser finnas i samma zon:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Principer för villkorlig åtkomst för kombinerad registrering

Att skydda när och hur användare registrerar sig för Azure Multi-Factor Authentication och återställning av lösen ord för självbetjäning är nu möjligt med användar åtgärder i princip för villkorlig åtkomst. Den här funktionen är tillgänglig för organisationer som har aktiverat [funktionen för kombinerad registrering](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan vara aktive rad i organisationer där de vill att användarna ska kunna registrera sig för Azure Multi-Factor Authentication och SSPR från en central plats, till exempel en betrott nätverks plats under en inledande registrering.

Mer information om hur du skapar betrodda platser i villkorlig åtkomst finns i artikeln [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip för att kräva registrering från en betrodd plats

Följande princip gäller för alla valda användare som försöker registrera sig med den kombinerade registrerings upplevelsen, och blockerar åtkomsten om de inte ansluter från en plats som har marker ATS som ett betrott nätverk.

1. I **Azure Portal**bläddrar du till **Azure Active Directory** > **säkerhet** > för**villkorlig åtkomst**
1. Välj **+ ny princip**
1. Ange ett namn för principen, till exempel *kombinerad säkerhets informations registrering på betrodda nätverk*.
1. Under **Tilldelningar** väljer du **Användare och grupper**. Välj de användare och grupper som du vill att den här principen ska tillämpas på och välj sedan **Slutför**.

   > [!WARNING]
   > Användare måste aktive ras för kombinerad registrering.

1. Under **molnappar eller åtgärder**väljer du **användar åtgärder**. Markera **Registrera säkerhets information**och välj sedan **OK**.

    ![Skapa en princip för villkorlig åtkomst för att kontrol lera registreringen av säkerhets information](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Konfigurera följande alternativ under **villkor** > **platser**:
   1. Konfigurera **Ja**
   1. Ta med **valfri plats**
   1. Undanta **alla betrodda platser**
1. Välj **färdig** i fönstret *platser* och välj sedan **färdig** i fönstret *villkor* .
1. Under **åtkomst kontroller** > **beviljas**väljer du **blockera åtkomst**och **väljer** sedan
1. Ange att principen ska **On** **aktive ras**
1. Om du vill slutföra principen väljer du **skapa**

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp kan du läsa så här [felsöker du en kombinerad registrering av säkerhets information](howto-registration-mfa-sspr-combined-troubleshoot.md) eller Lär dig [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?](../conditional-access/location-condition.md)

Om du vill aktivera funktionerna i din Azure AD-klient, se självstudierna för att [Aktivera självbetjäning för återställning av lösen ord](tutorial-enable-sspr.md) och [Aktivera Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Lär dig hur du [aktiverar kombinerad registrering i din klient organisation](howto-registration-mfa-sspr-combined.md) eller [tvinga användare att registrera autentiseringsmetoder på nytt](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Du kan också gå igenom [tillgängliga metoder för Azure Multi-Factor Authentication och SSPR](concept-authentication-methods.md).
