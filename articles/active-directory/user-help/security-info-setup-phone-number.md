---
title: Konfigurera säkerhetsinformation (förhandsversion) för att använda telefonsamtal – Azure Active Directory | Microsoft Docs
description: Hur du ställer in din säkerhetsinformation för att verifiera din identitet med telefonsamtal.
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
ms.openlocfilehash: 9c1620be30d8cdf3a592ab0fc118938783579689
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085090"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>Konfigurera säkerhetsinformation (förhandsversion) för att använda telefonsamtal
Du kan följa stegen nedan för att lägga till två faktorer verifieringen och metoder för återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till den **säkerhetsinformation** sidan för att lägga till, uppdatera eller ta bort din säkerhetsinformation.

Om du uppmanas att konfigurera detta omedelbart efter att du loggar in på ditt arbets- eller skolkonto konto finns detaljerade anvisningar i den [ställa in din säkerhetsinformation från inloggningssidan prompten](security-info-setup-signin.md) artikeln.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Säkerhetsinformation stöder inte med anknytningsnummer. Även om du lägger till rätt format, tas + 1 4255551234 X 12345 tilläggen bort innan de görs.
> 
> Om du inte ser en Telefonalternativet, är det möjligt att din organisation tillåter inte att du använder ett telefonnummer för att bekräfta. I det här fallet måste du välja en annan metod eller kontakta administratören för mer hjälp.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Konfigurera telefonsamtal från sidan säkerhetsinformation
Beroende på organisationens inställningar kan kanske du kan använda telefonsamtal som en av din information säkerhetsmetoder.

>[!Note]
>Om du vill få ett textmeddelande i stället för ett telefonsamtal, följer du stegen i den [konfigurera säkerhetsinformation för att använda textmeddelanden](security-info-setup-text-msg.md) artikeln.

### <a name="to-set-up-phone-calls"></a>Du ställer in telefonsamtal

1. Logga in på ditt arbets- eller skolkonto konto och gå sedan till din https://myprofile.microsoft.com/ sidan.

    ![Min profil-sidan som visar markerad Security info länkar](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhetsinformation** från det vänstra navigeringsfönstret eller från länken i den **säkerhetsinformation** blockera och sedan välja **tilläggsmetod** från den **säkerhetsinformation**  sidan.

    ![Sidan för Security information med markerade Lägg till metodalternativet](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På den **Lägg till en metod** väljer **Phone** från listrutan och välj sedan **Lägg till**.

    ![Lägg till metoden rutan med telefon som har valts](media/security-info/securityinfo-myprofile-addphonetext.png)

4. På den **Phone** sidan, anger du telefonnumret för din mobila enhet, Välj **ringa mig**, och välj sedan **nästa**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Besvara samtalet verifiering skickas till det telefonnummer som du har angett och följ instruktionerna.

    Sidändringar att visa din framgång.

    ![Meddelande för lyckade, ansluter telefonen number, möjlighet att ta emot samtal, och ditt konto](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Din säkerhetsinformation uppdateras och du kan använda telefonsamtal för att verifiera din identitet när du använder återställning för tvåstegsverifiering verifiering eller lösenord. Om du vill ringa telefonsamtal din standardmetod finns i den [ändra standard security info metoden](#change-your-default-security-info-method) i den här artikeln.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Ta bort telefonsamtal från din info säkerhetsmetoder
Om du inte längre vill använda telefonsamtal som en säkerhetsmetod information du kan ta bort den från den **säkerhetsinformation** sidan.

>[!Important]
>Om du tar bort telefonsamtal av misstag, går det inte att ångra åtgärden. Du måste lägga till metoden igen, följa stegen i den [konfigurera telefonsamtal](#set-up-phone-calls-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-phone-calls"></a>Ta bort telefonsamtal

1. På den **säkerhetsinformation** väljer den **ta bort** länka bredvid den **Phone** alternativet.

    ![Länk för att ta bort metoden phone från säkerhetsinformation](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Välj **Ja** från dialogrutan Bekräfta att ta bort den **Phone** tal. När ditt telefonnummer har tagits bort, tas den bort från din säkerhetsinformation och försvinner den från den **säkerhetsinformation** sidan. Om **Phone** är din standardmetod standard ändras till en annan metod som är tillgängliga.
    
## <a name="change-your-default-security-info-method"></a>Ändra din standardmetod security info
Om du vill ringa samtal ska vara den standardmetod som används när du loggar in till ditt arbets- eller skolkonto konto med två faktorer verifiering eller för att återställa lösenord begäranden, kan du ändra det från den **säkerhetsinformation** sidan.

### <a name="to-change-your-default-security-info-method"></a>Ändra din standardmetod security info

1. På den **säkerhetsinformation** väljer den **ändra** länka bredvid den **standard inloggningsmetod** information.

    ![Ändra-länk för standard inloggningsmetod](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Välj **Phone - anrop (*_your_phone_number_*)** från listan över tillgängliga metoder och välj sedan **Bekräfta**.

    ![Välj metod för att logga in standard](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    Standardmetod som används för inloggning ändringar i **Phone - anrop (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Ytterligare information om säkerhetsmetoder
Du har ytterligare alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-appen.** Ladda ned och använda en autentiseringsapp för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännandekod för återställning av tvåstegsverifiering verifiering eller lösenord. Stegvisa instruktioner om hur du skapar och använder Microsoft Authenticator-appen finns i [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Text för mobila enheter.** Ange enhetsnumret till din mobila och få ett textmeddelande när en kod som du använder för tvåstegsverifiering eller lösenord återställa. Stegvisa instruktioner om hur du verifierar din identitet med hjälp av ett textmeddelande (SMS) finns i [konfigurera säkerhetsinformation för att använda textmeddelanden (SMS)](security-info-setup-text-msg.md).

- **E-postadress.** Ange företagets eller skolans e-postadress för att få ett e-postmeddelande för återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in din e-post finns i [konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

- **Säkerhetsfrågor.** Svara på vissa säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgänglig för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i den [konfigurera säkerhetsinformation kan använda säkerhetsfrågor](security-info-setup-questions.md) artikeln.
    
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).