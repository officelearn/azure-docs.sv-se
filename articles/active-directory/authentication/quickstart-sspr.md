---
title: Snabb start – återställning av lösen ord för självbetjäning i Azure AD
description: I den här snabb starten lär du dig att konfigurera lösen ords återställning via självbetjäning i Azure AD så att användarna kan återställa sina egna lösen ord och minska IT-avdelningens kostnader.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168f9bf58c4942fc0b76b9ffefc2b32b5bfbe5a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549371"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>Snabb start: Konfigurera Azure Active Directory återställning av lösen ord för självbetjäning

I den här snabb starten konfigurerar du Azure Active Directory (AD) självbetjäning för återställning av lösen ord (SSPR) för att göra det möjligt för användare att återställa sina lösen ord eller låsa upp sina konton. Med SSPR kan användare återställa sina egna autentiseringsuppgifter utan supportavdelningen eller administratörs hjälp. Med den här möjligheten kan användarna få åtkomst till sitt konto utan att behöva vänta på ytterligare support.

> [!IMPORTANT]
> Den här snabb starten visar en administratör för att aktivera återställning av lösen ord för självbetjäning. Om du är en slutanvändare som redan är registrerad för lösen ords återställning via självbetjäning och behöver gå tillbaka till ditt konto går du till https://aka.ms/sspr.
>
> Om IT-teamet inte har aktiverat möjligheten att återställa ditt eget lösen ord kan du kontakta supportavdelningen för ytterligare hjälp.

## <a name="prerequisites"></a>Krav

* En aktiv Azure AD-klientorganisation med minst en aktiverad utvärderingslicens.
    * Om det behövs kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett konto med behörigheter som global administratör.
* En icke-administratörs test användare med ett lösen ord som du känner till, till exempel *testuser*.
    * Om du behöver skapa en användare, se [snabb start: Lägg till nya användare i Azure Active Directory](../add-users-azure-active-directory.md).
* En pilot grupp som ska testas med den användare som inte är administratörs test är medlem i, till exempel *SSPR-test-Group*.
    * Om du behöver skapa en grupp, se så här [skapar du en grupp och lägger till medlemmar i Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

[Visa den här processen som en video på YouTube](https://youtu.be/Pa0eyqjEjvQ)

1. I Azure Portal-menyn eller på **Start** sidan väljer du **Azure Active Directory** och väljer sedan **lösen ords återställning**.

1. Välj **vald**på sidan **Egenskaper** under alternativet för **återställning av lösen ord**för självbetjäning aktiverat.
1. Välj **Välj grupp**och välj sedan den pilot grupp som skapats som en del av avsnittet krav i den här artikeln, till exempel *SSPR-test-Group*. När du är klar väljer du **Spara**.
1. På sidan **autentiseringsmetoder** gör du följande val och väljer sedan **Spara**:
    * Antal metoder som krävs för återställning: **1**
    * Metoder som är tillgängliga för användare:
        * **Kod för mobilapp**
        * **E-post**

    > [!div class="mx-imgBorder"]
    > ![Välja autentiseringsmetoder för SSPR][Authentication]

4. På sidan **registrering** gör du följande val och väljer sedan **Spara**:
   * Kräv att användare registrerar sig vid inloggning: **Ja**
   * Ange antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation: **365**

## <a name="test-self-service-password-reset"></a>Testa självåterställning av lösenord

Nu kan du testa din SSPR-konfiguration med en test användare som är en del av gruppen som du valde i föregående avsnitt, till exempel *testuser*. Eftersom Microsoft tillämpar starka autentiseringskrav för administratörskonton i Azure kan det hända att resultatet ändras efter testning med administratörskonto. Mer information om lösenordsprinciper för administratörer finns i vår artikel om [lösenordsprinciper](concept-sspr-policy.md).

1. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläge och gå till [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
2. Logga in med en icke-administratörs test användare, till exempel *testuser*, och registrera din telefon för autentisering.
3. När du är klar väljer du knappen **ser bra ut** och stänger webbläsarfönstret.
4. Öppna ett nytt webbläsarfönster i InPrivate- eller inkognitoläge och gå till [https://aka.ms/sspr](https://aka.ms/sspr).
5. Ange användarens användar-ID som icke-administratörs test, till exempel *testuser*, tecknen från captcha och välj sedan **Nästa**.
6. Följ verifierings stegen för att återställa lösen ordet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill inaktivera lösen ords återställning via självbetjäning söker du efter och väljer **Azure Active Directory** i Azure Portal. Välj **egenskaper** > **lösen ords återställning**och välj sedan **ingen** under **återställning av lösen ord för självbetjäning aktiverat**. När du är klar väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du konfigurerar lösen ords återställning via självbetjäning för dina användare i molnet. Om du vill ta reda på hur du utför en mer detaljerad lansering fortsätter du till lanseringsguiden.

> [!div class="nextstepaction"]
> [Lansera självåterställning av lösenord](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "Azure AD-autentiseringsmetoder som är tillgängliga och den kvantitet som krävs"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
