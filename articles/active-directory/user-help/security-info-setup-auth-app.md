---
title: Konfigurera säkerhetsinformation (förhandsversion) för att använda en autentiseringsapp - Azure Active Directory | Microsoft Docs
description: Hur du ställer in din säkerhetsinformation för att verifiera din identitet med hjälp av Microsoft Authenticator-appen.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4757be00a3633f56aed52dd7af22923e49b0b62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60475724"
---
# <a name="set-up-security-info-preview-to-use-an-authenticator-app"></a>Konfigurera säkerhetsinformation (förhandsversion) för att använda en autentiseringsapp
Du kan följa stegen nedan för att lägga till två faktorer verifieringen och metoder för återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till den **säkerhetsinformation** sidan för att lägga till, uppdatera eller ta bort din säkerhetsinformation.

Om du uppmanas att konfigurera detta omedelbart efter att du loggar in på ditt arbets- eller skolkonto konto finns detaljerade anvisningar i den [ställa in din säkerhetsinformation från inloggningssidan prompten](security-info-setup-signin.md) artikeln.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Om du inte ser en authenticator-appalternativet, är det möjligt att din organisation tillåter inte att du använder en authentication-appen för att bekräfta. I det här fallet måste du välja en annan metod eller kontakta administratören för mer hjälp.

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Konfigurera Microsoft Authenticator-appen från sidan säkerhetsinformation
Beroende på organisationens inställningar kan kanske du använda en authentication-appen som en av din information säkerhetsmetoder. Du behöver inte använda Microsoft Authenticator-appen och du kan välja en annan app under installationen. Den här artikeln använder dock Microsoft Authenticator-appen. 

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Konfigurera Microsoft Authenticator-appen

1. Logga in på ditt arbets- eller skolkonto konto och gå sedan till din https://myprofile.microsoft.com/ sidan.

    ![Min profil-sidan som visar markerad Security info länkar](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhetsinformation** från det vänstra navigeringsfönstret eller från länken i den **säkerhetsinformation** blockera och sedan välja **tilläggsmetod** från den **säkerhetsinformation**  sidan.

    ![Sidan för Security information med markerade Lägg till metodalternativet](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På den **Lägg till en metod** väljer **autentiseringsapp** från listrutan och välj sedan **Lägg till**.

    ![Lägg till metoden rutan med Authenticator-app har valts](media/security-info/securityinfo-myprofile-addauthapp.png)

4. På den **börja med att hämta appen** väljer **Hämta nu** att ladda ned och installera Microsoft Authenticator-appen på din mobila enhet och välj sedan **nästa**.

    Mer information om hur du laddar ned och installerar appen finns i [Ladda ned och installera Microsoft Authenticator-appen](user-help-auth-app-download-install.md).

    ![Börja med att hämta sidan](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Om du vill använda en annan autentiseringsapp än Microsoft Authenticator-appen väljer du länken **Jag vill använda en annan autentiseringsapp**.
   > 
   > Om din organisation låter dig välja en annan metod än autentiseringsappen kan du välja länken **Jag vill konfigurera en annan metod**.

5. Ha sidan **Konfigurera ditt konto** öppen medan du konfigurerar Microsoft Authenticator-appen på din mobila enhet.

    ![Ställ in sidan authenticator](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Öppna Microsoft Authenticator-appen, välj att tillåta meddelanden (om du uppmanas), välj **Lägg till konto** från ikonen **Customize and control** (Anpassa och styr) uppe till höger och välj sedan **Arbets- eller skolkonto**.

7. Gå tillbaka till den **konfigurerar ditt konto** sidan på din, och välj sedan **nästa**.

    Sidan **Skanna QR-koden** visas.

    ![Skanna QR-koden med hjälp av Authenticator-appen](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

6. Skanna koden som tillhandahålls med Microsoft Authenticator-appen QR-kod reader, som fanns på din mobila enhet när du har skapat ditt arbets- eller skolkonto konto i steg 6.

    Authenticator-appen bör lägga till ditt arbets- eller skolkonto utan att någon ytterligare information krävs från dig. Men om QR-kodläsaren inte kan läsa koden kan du välja länken **Jag kan inte skanna QR-koden** och manuellt ange koden och URL:en i Microsoft Authenticator-appen. Mer information om att lägga till en kod manuellt finns i [Lägga till ett konto i appen manuellt](user-help-auth-app-add-account-manual.md).

7. Välj **nästa** på den **skanna QR-koden** sidan på din.

    Ett meddelande skickas till Microsoft Authenticator-appen på din mobila enhet för att testa ditt konto.

    ![Testa ditt konto med Authenticator-appen](media/security-info/securityinfo-myprofile-tryitauthapp.png)

8. Godkänn meddelandet i Microsoft Authenticator-appen och välj sedan **Nästa**.

     ![Meddelande om att det lyckades; appen och ditt konto ansluts](media/security-info/securityinfo-myprofile-successauthapp.png)

     Din säkerhetsinformation uppdateras till att använda Microsoft Authenticator-appen som standard för att verifiera din identitet vid användning av tvåstegsverifiering eller återställning av lösenord.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Ta bort din authenticator-app från din info säkerhetsmetoder
Om du inte längre vill använda din autentiserare som en säkerhetsmetod information du kan ta bort den från den **säkerhetsinformation** sidan. Detta fungerar för alla authenticator-appar, inte bara Microsoft Authenticator-appen. När du tar bort appen, har du gå till autentiseringsappen på din mobila enhet och ta bort kontot.

>[!Important]
>Om du tar bort autentiseringsappen av misstag, går det inte att ångra åtgärden. Du måste lägga till authenticator-appen igen, följa stegen i den [konfigurera autentiseringsappen](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-the-authenticator-app"></a>Ta bort authenticator-appen

1. På den **säkerhetsinformation** väljer den **ta bort** länken bredvid Authenticator-appen.

    ![Länk för att ta bort authenticator-appen från säkerhetsinformation](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Välj **Ja** från dialogrutan Bekräfta att ta bort authenticator-appen. När authenticator-appen har tagits bort, tas den bort från din säkerhetsinformation och försvinner den från den **säkerhetsinformation** sidan. Om authenticator-appen är din standardmetod, ändras standardvärdet till en annan metod som är tillgängliga.

3. Öppna autentiseringsappen på din mobila enhet, väljer **redigera konton**, och ta sedan bort arbets-eller skolkonto från authenticator-appen.

    Ditt konto helt tas bort från authenticator-appen för tvåfaktorsautentisering verifiering och begäranden för återställning av lösenord.

## <a name="change-your-default-security-info-method"></a>Ändra din standardmetod security info
Om du vill authenticator-appen är den standardmetod som används när du loggar in till ditt arbets- eller skolkonto konto med två faktorer verifiering eller för att återställa lösenord begäranden måste du ange den från säkerheten **info** sidan.

### <a name="to-change-your-default-security-info-method"></a>Ändra din standardmetod security info

1. På den **säkerhetsinformation** väljer den **ändra** länka bredvid den **standard inloggningsmetod** information.

    ![Ändra-länk för standard inloggningsmetod](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Välj **Microsoft Authenticator - meddelande** från den nedrullningsbara listan över tillgängliga metoder. Om du inte använder Microsoft Authenticator-appen, väljer du den **Authenticator-appen eller maskinvara token** alternativet.

    ![Välj metod för att logga in standard](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Välj **Bekräfta**.

    Standardmetod som används för inloggning ändringar i Microsoft Authenticator-appen.

## <a name="additional-security-info-methods"></a>Ytterligare information om säkerhetsmetoder
Du har ytterligare alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Text för mobila enheter.** Ange enhetsnumret till din mobila och få ett textmeddelande när en kod som du använder för tvåstegsverifiering eller lösenord återställa. Stegvisa instruktioner om hur du verifierar din identitet med hjälp av ett textmeddelande (SMS) finns i [konfigurera säkerhetsinformation för att använda textmeddelanden (SMS)](security-info-setup-text-msg.md).

- **Mobil enhet eller arbete telefonsamtal.** Ange enhetsnumret till din mobila och få ett telefonsamtal för verifiering eller lösenord återställning av två steg. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **E-postadress.** Ange företagets eller skolans e-postadress för att få ett e-postmeddelande för återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in din e-post finns i [konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

- **Säkerhetsfrågor.** Svara på vissa säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgänglig för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i den [konfigurera säkerhetsinformation kan använda säkerhetsfrågor](security-info-setup-questions.md) artikeln.
    
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).