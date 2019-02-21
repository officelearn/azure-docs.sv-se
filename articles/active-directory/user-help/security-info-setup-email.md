---
title: Konfigurera säkerhetsinformation (förhandsversion) för att använda din e-postadress – Azure Active Directory | Microsoft Docs
description: Hur du ställer in din säkerhetsinformation för att verifiera din identitet med din e-postadress.
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
ms.openlocfilehash: 7c6bf2c8ea6a2526f8548a785706e5ed3a7460ff
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455219"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Konfigurera säkerhetsinformation (förhandsversion) för att använda din e-postadress
Du kan följa stegen nedan för att lägga till metoden för återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till den **säkerhetsinformation** sidan för att lägga till, uppdatera eller ta bort din säkerhetsinformation.

När du har konfigurerat metoden för återställning av lösenord, du måste också ställa in din tvåfaktorsautentisering verifieringsmetod, med hjälp av en [autentiseringsapp](security-info-setup-auth-app.md), [textmeddelanden](security-info-setup-text-msg.md), eller en [telefonsamtal](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Ange din e-postadress från sidan säkerhetsinformation
Beroende på organisationens inställningar kan kanske du kan använda din e-postadress som en av din information säkerhetsmetoder.

>[!Note]
>Vi rekommenderar att du använder en e-postadress som inte kräver nätverkslösenordet för att få åtkomst till. Om du inte ser alternativet e-post, är det möjligt att din organisation tillåter inte att du använder ett e-postmeddelande för verifiering. Om så är fallet, måste du välja en annan metod eller kontakta administratören för mer hjälp.

### <a name="to-set-up-your-email-address"></a>Du ställer in din e-postadress

1. Logga in på ditt arbets- eller skolkonto konto och gå sedan till din https://myprofile.microsoft.com/ sidan.

    ![Min profil-sidan som visar markerad Security info länkar](media/security-info/securityinfo-myprofile.png)

2. Välj **säkerhetsinformation** från det vänstra navigeringsfönstret eller från länken i den **säkerhetsinformation** blockera och sedan välja **tilläggsmetod** från den **säkerhetsinformation**  sidan.

    ![Sidan för Security information med markerade Lägg till metodalternativet](media/security-info/securityinfo-myprofile-addmethod.png)

3. På den **Lägg till en metod** väljer **e-post** från listrutan och välj sedan **Lägg till**.

    ![Lägg till metoden rutan med e-postadress som valts](media/security-info/securityinfo-myprofile-addemail.png)

4. På den **e-post** , ange din e-postadress (till exempel alain@gmail.com), och välj sedan **nästa**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >E-postadressen får inte vara din arbets- eller skolkonto e-post.

5. Skriv kod som skickas till din angivna e-postadress och välj sedan **nästa**.

    ![Lägg till telefonnummer och välj textmeddelanden](media/security-info/securityinfo-myprofile-emailcode.png)

    Din säkerhetsinformation uppdateras och du kan använda din e-postadress för att verifiera din identitet när du använder återställning av lösenord.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Ta bort din e-postadress från din info säkerhetsmetoder
Om du inte längre vill använda din e-postadress som en säkerhetsmetod information du kan ta bort den från den **säkerhetsinformation** sidan.

>[!Important]
>Om du tar bort din e-postadress av misstag, går det inte att ångra åtgärden. Du måste lägga till metoden igen, följa stegen i den [ställa in din e-postadress](#set-up-your-email-address-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-your-email-address"></a>Att ta bort din e-postadress

1. På den **säkerhetsinformation** väljer den **ta bort** länka bredvid den **e-post** alternativet.

    ![Länk för att ta bort metoden phone från säkerhetsinformation](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Välj **Ja** från dialogrutan Bekräfta att ta bort den **e-post** konto. När e-postkonto tas bort, tas den bort från din säkerhetsinformation och försvinner den från den **säkerhetsinformation** sidan.

## <a name="additional-security-info-methods"></a>Ytterligare information om säkerhetsmetoder
Du har ytterligare alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-appen.** Ladda ned och använda en autentiseringsapp för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännandekod för återställning av tvåstegsverifiering verifiering eller lösenord. Stegvisa instruktioner om hur du skapar och använder Microsoft Authenticator-appen finns i [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Text för mobila enheter.** Ange enhetsnumret till din mobila och få ett textmeddelande när en kod som du använder för tvåstegsverifiering eller lösenord återställa. Stegvisa instruktioner om hur du verifierar din identitet med hjälp av ett textmeddelande (SMS) finns i [konfigurera säkerhetsinformation för att använda textmeddelanden (SMS)](security-info-setup-text-msg.md).

- **Mobil enhet eller arbete telefonsamtal.** Ange enhetsnumret till din mobila och få ett telefonsamtal för verifiering eller lösenord återställning av två steg. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **Säkerhetsfrågor.** Svara på vissa säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgänglig för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i den [konfigurera säkerhetsinformation kan använda säkerhetsfrågor](security-info-setup-questions.md) artikeln.
    
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följer du stegen i den [återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md) artikeln.

- Hämta felsöka tips och hjälp med inloggningsproblem i den [kan inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.
