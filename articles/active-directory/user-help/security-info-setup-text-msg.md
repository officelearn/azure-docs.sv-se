---
title: Konfigurera säkerhetsinformation (förhandsversion) du använder textmeddelanden - Azure Active Directory | Microsoft Docs
description: Hur du ställer in din säkerhetsinformation för att verifiera din identitet med hjälp av textmeddelanden och din mobila enhet.
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
ms.openlocfilehash: 00236a810fb85be6af914021acec2d2b87698e1d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588593"
---
# <a name="set-up-security-info-preview-to-use-text-messaging"></a>Konfigurera säkerhetsinformation (förhandsversion) för att använda textmeddelanden
Du kan följa stegen nedan för att lägga till två faktorer verifieringen och metoder för återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till den **säkerhetsinformation** sidan för att lägga till, uppdatera eller ta bort din säkerhetsinformation.

Om du uppmanas att konfigurera detta omedelbart efter att du loggar in på ditt arbets- eller skolkonto konto finns detaljerade anvisningar i den [ställa in din säkerhetsinformation från inloggningssidan prompten](security-info-setup-signin.md) artikeln.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Om du inte ser en Telefonalternativet, är det möjligt att din organisation tillåter inte att du använder ett telefonnummer för att bekräfta. I det här fallet måste du välja en annan metod eller kontakta administratören för mer hjälp.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Konfigurera textmeddelanden från sidan säkerhetsinformation
Beroende på organisationens inställningar kan kanske du kan använda textmeddelanden som en av din information säkerhetsmetoder. Alternativet text är en del av Telefonalternativet, så att du kan ange allt in på samma sätt som du skulle för ditt telefonnummer, men i stället för att låta Microsoft ringer upp dig, du väljer att använda ett textmeddelande.

>[!Note]
>Om du vill få ett telefonsamtal i stället för ett textmeddelande, följer du stegen i den [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md) artikeln.

### <a name="to-set-up-text-messages"></a>Konfigurera textmeddelanden

1. Logga in på ditt arbets- eller skolkonto konto och gå sedan till din https://myprofile.microsoft.com/ sidan.

    ![Min profil-sidan som visar markerad Security info länkar](media/security-info/securityinfo-myprofile.png)

2. Välj **säkerhetsinformation** från det vänstra navigeringsfönstret eller från länken i den **säkerhetsinformation** blockera och sedan välja **tilläggsmetod** från den **säkerhetsinformation**  sidan.

    ![Sidan för Security information med markerade Lägg till metodalternativet](media/security-info/securityinfo-myprofile-addmethod.png)

3. På den **Lägg till en metod** väljer **Phone** från listrutan och välj sedan **Lägg till**.

    ![Lägg till metoden rutan med telefon som har valts](media/security-info/securityinfo-myprofile-addphonetext.png)

4. På den **Phone** sidan, anger du telefonnumret för din mobila enhet, Välj **SMS: a mig en kod**, och välj sedan **nästa**.

    ![Lägg till telefonnummer och välj textmeddelanden](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Ange den kod som skickats till dig via SMS till din mobila enhet och välj sedan **nästa**.

    ![Lägg till telefonnummer och välj textmeddelanden](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Sidändringar att visa din framgång.

    ![Meddelande för lyckade, ansluter telefonen number, möjlighet att ta emot text messags och ditt konto](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Din säkerhetsinformation uppdateras och du kan använda textmeddelanden för att verifiera din identitet när du använder återställning för tvåstegsverifiering verifiering eller lösenord. Om du vill göra din standardmetod för textmeddelanden, finns i den [ändra standard security info metoden](#change-your-default-security-info-method) i den här artikeln.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Ta bort textmeddelanden från din info säkerhetsmetoder
Om du inte längre vill använda textmeddelanden som en säkerhetsmetod information du kan ta bort den från den **säkerhetsinformation** sidan.

>[!Important]
>Om du tar bort av misstag textmeddelanden, går det inte att ångra åtgärden. Du måste lägga till metoden igen, följa stegen i den [konfigurera textmeddelanden](#set-up-text-messages-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-text-messaging"></a>Att ta bort textmeddelanden

1. På den **säkerhetsinformation** väljer den **ta bort** länka bredvid den **Phone** alternativet.

    ![Länk för att ta bort telefonen och metod som säkerhetsinformation för textmeddelanden](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Välj **Ja** från dialogrutan Bekräfta att ta bort den **Phone** tal. När ditt telefonnummer har tagits bort, tas den bort från din säkerhetsinformation och försvinner den från den **säkerhetsinformation** sidan. Om **Phone** är din standardmetod standard ändras till en annan metod som är tillgängliga.

## <a name="change-your-default-security-info-method"></a>Ändra din standardmetod security info
Om du vill textmeddelanden för att vara den standardmetod som används när du loggar in till ditt arbets- eller skolkonto konto med två faktorer verifiering eller för att återställa lösenord begäranden, kan du ändra det från den **säkerhetsinformation** sidan.

### <a name="to-change-your-default-security-info-method"></a>Ändra din standardmetod security info

1. På den **säkerhetsinformation** väljer den **ändra** länka bredvid den **standard inloggningsmetod** information.

    ![Ändra-länk för standard inloggningsmetod](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Välj **Phone - text (*_your_phone_number_*)** från listan över tillgängliga metoder och välj sedan **Bekräfta**.

    ![Välj metod för att logga in standard](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Standardmetod som används för inloggning ändringar i **Phone - text (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Ytterligare information om säkerhetsmetoder
Du har ytterligare alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-appen.** Ladda ned och använda en autentiseringsapp för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännandekod för återställning av tvåstegsverifiering verifiering eller lösenord. Stegvisa instruktioner om hur du skapar och använder Microsoft Authenticator-appen finns i [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Mobil enhet eller arbete telefonsamtal.** Ange enhetsnumret till din mobila och få ett telefonsamtal för verifiering eller lösenord återställning av två steg. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **E-postadress.** Ange företagets eller skolans e-postadress för att få ett e-postmeddelande för återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in din e-post finns i [konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

- **Säkerhetsfrågor.** Svara på vissa säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgänglig för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i den [konfigurera säkerhetsinformation kan använda säkerhetsfrågor](security-info-setup-questions.md) artikeln.
    
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följer du stegen i den [återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md) artikeln.

- Hämta felsöka tips och hjälp med inloggningsproblem i den [kan inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.