---
title: Konfigurera Microsoft Authenticator-appen som verifieringsmetod – Azure AD
description: Konfigurera sidan Säkerhetsinformation (förhandsversion) för att verifiera din identitet med Hjälp av Microsoft Authenticator-appen som verifieringsmetod.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 16cb512d3f8f8afcc199cb52c13e09b12107576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062429"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Konfigurera Microsoft Authenticator-appen som verifieringsmetod

Du kan följa dessa steg för att lägga till dina metoder för tvåfaktorsverifiering och återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till sidan **Säkerhetsinformation** för att lägga till, uppdatera eller ta bort säkerhetsinformationen.

Om du uppmanas att konfigurera detta direkt efter att du har loggat in på ditt arbets- eller skolkonto läser du de detaljerade stegen i dialogrutan Konfigurera din säkerhetsinformation från snabb artikeln [för inloggningssidan.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Om du inte ser alternativet autentiseringsapp är det möjligt att din organisation inte tillåter att du använder det här alternativet för verifiering. I det här fallet måste du välja en annan metod eller kontakta organisationens supportavdelning för mer hjälp.

## <a name="security-vs-password-reset-verification"></a>Säkerhet vs lösenordsåterställningsverifiering

Metoder för säkerhetsinformation används för både tvåfaktorssäkerhetsverifiering och för återställning av lösenord. Dock kan inte alla metoder användas för båda.

| Metod | Används för |
| ------ | -------- |
| Autentiseringsapp | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| SMS | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Telefonsamtal | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Säkerhetsnyckel | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| E-postkonto | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |
| Säkerhetsfrågor | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Konfigurera Microsoft Authenticator-appen från sidan Säkerhetsinformation

Beroende på organisationens inställningar kanske du kan använda en autentiseringsapp som en av dina metoder för säkerhetsinformation. Du behöver inte använda Microsoft Authenticator-appen och du kan välja en annan app under installationen. I den här artikeln används dock Microsoft Authenticator-appen.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Så här konfigurerar du Microsoft Authenticator-appen

1. Logga in på ditt arbets- eller https://myprofile.microsoft.com/ skolkonto och gå sedan till din sida.

    ![Sidan Min profil, med markerade länkar för säkerhetsinformation](media/security-info/securityinfo-myprofile-page.png)

2. Välj **Säkerhetsinformation** i det vänstra navigeringsfönstret eller från länken i blocket **Säkerhetsinformation** och välj sedan **Lägg till metod** på sidan **Säkerhetsinformation.**

    ![Sidan Säkerhetsinformation med markerat alternativ för Lägg till metod](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du Appen **Autentiserare** i listrutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod med appen Authenticator markerad](media/security-info/securityinfo-myprofile-addauthapp.png)

4. På **sidan Starta med att hämta appsidan** väljer du **Ladda ned nu** för att hämta och installera Microsoft Authenticator-appen på din mobila enhet och väljer sedan **Nästa**.

    Mer information om hur du laddar ned och installerar appen finns i [Ladda ned och installera Microsoft Authenticator-appen](user-help-auth-app-download-install.md).

    ![Börja med att hämta appsidan](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Om du vill använda en annan autentiseringsapp än Microsoft Authenticator-appen väljer du länken **Jag vill använda en annan autentiseringsapp**.
   >
   > Om din organisation låter dig välja en annan metod än autentiseringsappen kan du välja länken **Jag vill konfigurera en annan metod**.

5. Ha sidan **Konfigurera ditt konto** öppen medan du konfigurerar Microsoft Authenticator-appen på din mobila enhet.

    ![Konfigurera sidan för autentiseringsappen](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Öppna Microsoft Authenticator-appen, välj att tillåta meddelanden (om du uppmanas), välj **Lägg till konto** från ikonen **Customize and control** (Anpassa och styr) uppe till höger och välj sedan **Arbets- eller skolkonto**.

    >[!Note]
    >Om det är första gången du konfigurerar Microsoft Authenticator-appen kan du få en uppmaning om du vill tillåta appen att komma åt kameran (iOS) eller låta appen ta bilder och spela in video (Android). Du måste välja **Tillåt** så att autentiseringsappen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera autentiseringsappen, men du måste lägga till kodinformationen manuellt. Information om hur du lägger till koden manuellt finns i [Lägg till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

7. Gå tillbaka till sidan **Konfigurera ditt konto** på datorn och välj sedan **Nästa**.

    Sidan **Skanna QR-koden** visas.

    ![Skanna QR-koden med hjälp av Authenticator-appen](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Skanna den medföljande koden med QR-kodläsaren i Microsoft Authenticator-appen, som visades på din mobila enhet efter att du skapat ditt arbets- eller skolkonto i steg 6.

    Authenticator-appen bör lägga till ditt arbets- eller skolkonto utan att någon ytterligare information krävs från dig. Men om QR-kodläsaren inte kan läsa koden kan du välja länken **Jag kan inte skanna QR-koden** och manuellt ange koden och URL:en i Microsoft Authenticator-appen. Mer information om att lägga till en kod manuellt finns i [Lägga till ett konto i appen manuellt](user-help-auth-app-add-account-manual.md).

9. Välj **Nästa** på sidan **Skanna QR-koden** på datorn.

    Ett meddelande skickas till Microsoft Authenticator-appen på din mobila enhet för att testa ditt konto.

    ![Testa ditt konto med Authenticator-appen](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Godkänn meddelandet i Microsoft Authenticator-appen och välj sedan **Nästa**.

     ![Meddelande om att det lyckades; appen och ditt konto ansluts](media/security-info/securityinfo-myprofile-successauthapp.png)

     Din säkerhetsinformation uppdateras till att använda Microsoft Authenticator-appen som standard för att verifiera din identitet vid användning av tvåstegsverifiering eller återställning av lösenord.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Ta bort din autentiserarapp från dina metoder för säkerhetsinformation

Om du inte längre vill använda autentiseringsappen som en säkerhetsinformationsmetod kan du ta bort den från sidan **Säkerhetsinformation.** Detta fungerar för alla autentiseringsappar, inte bara För Microsoft Authenticator-appen. När du har tagit bort appen måste du gå in i autentiseringsappen på din mobila enhet och ta bort kontot.

>[!Important]
>Om du tar bort autentiseringsappen av misstag kan du inte ångra den. Du måste lägga till autentiseringsappen igen, i följd av stegen i avsnittet [Konfigurera autentiseringsappen](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-the-authenticator-app"></a>Så här tar du bort autentiseringsappen

1. På sidan **Säkerhetsinformation** väljer du länken **Ta bort bredvid** Autentiseringsappen.

    ![Länk för att ta bort autentiseringsappen från säkerhetsinformation](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Välj **Ja** i bekräftelserutan om du vill ta bort autentiseringsappen. När autentiseringsappen har tagits bort tas den bort från din säkerhetsinformation och försvinner från sidan **Säkerhetsinformation.** Om autentiseringsappen är standardmetoden ändras standardinställningen till en annan tillgänglig metod.

3. Öppna autentiseringsappen på din mobila enhet, välj **Redigera konton**och ta sedan bort ditt arbets- eller skolkonto från autentiseringsappen.

    Ditt konto tas bort helt från autentiseringsappen för tvåfaktorsverifierings- och lösenordsåterställningsbegäranden.

## <a name="change-your-default-security-info-method"></a>Ändra standardmetoden för säkerhetsinformation

Om du vill att autentiseringsappen ska vara den standardmetod som används när du loggar in på ditt arbets- eller skolkonto med tvåfaktorsverifiering eller för begäranden om återställning av lösenord kan du ange den från sidan **Säkerhetsinformation.**

### <a name="to-change-your-default-security-info-method"></a>Så här ändrar du standardmetoden för säkerhetsinformation

1. På sidan **Säkerhetsinformation** väljer du länken **Ändra** bredvid standardinformationen för **inloggningsmetod.**

    ![Ändra länk för standard inloggningsmetod](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Välj **Microsoft Authenticator – meddelande** i listrutan med tillgängliga metoder. Om du inte använder Microsoft Authenticator-appen väljer du alternativet **Autentiseringsapp eller maskinvarutoken.**

    ![Välj metod för standard inloggning](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Välj **Bekräfta**.

    Standardmetoden som används för inloggningsändringar i Microsoft Authenticator-appen.

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhetsinformation

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad du försöker göra. Följande alternativ är tillgängliga:

- **Text för mobila enheter.** Ange ditt mobilenhetsnummer och få en text som du använder för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett SMS finns i [Konfigurera säkerhetsinformation för att använda SMS](security-info-setup-text-msg.md).

- **Mobil enhet eller arbetssamtal.** Ange ditt mobilenhetsnummer och få ett telefonsamtal för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **Säkerhetsnyckel.** Registrera din Microsoft-kompatibla säkerhetsnyckel och använd den tillsammans med en PIN-kod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med en säkerhetsnyckel finns i [Konfigurera säkerhetsinformation för att använda en säkerhetsnyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress för arbete eller skola för att få ett e-postmeddelande om återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

- **Säkerhetsfrågor.** Svara på några säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgängligt för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i artikeln [Konfigurera säkerhetsinformation för att använda säkerhetsfrågor.](security-info-setup-questions.md)

    >[!Note]
    >Om några av dessa alternativ saknas beror det troligen på att organisationen inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Logga in med Appen Microsoft Authenticator, i följande steg i inloggningen med hjälp [av tvåstegsverifiering eller säkerhetsinformation.](security-info-setup-signin.md)

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
