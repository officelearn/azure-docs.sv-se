---
title: Konfigurera säkerhets information (för hands version) för att använda telefonsamtal – Azure Active Directory | Microsoft Docs
description: Hur du konfigurerar din säkerhets information för att verifiera din identitet med hjälp av telefonsamtal.
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
ms.openlocfilehash: fbac4f364dedc28f7d5f3e100481a56bde70b4d3
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382922"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>Konfigurera säkerhets information (för hands version) för att använda telefonsamtal

Du kan följa de här stegen för att lägga till dina metoder för verifiering och återställning av lösen ord. När du har konfigurerat den första gången kan du gå tillbaka till sidan **säkerhets** information för att lägga till, uppdatera eller ta bort din säkerhets information.

Om du uppmanas att ställa in det här direkt efter att du har loggat in på ditt arbets-eller skol konto, se anvisningarna i artikeln [Konfigurera din säkerhets information från inloggnings sidan](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Säkerhets information har inte stöd för användning av telefon tillägg. Även om du lägger till rätt format, + 1 4255551234X12345, tas tilläggen bort innan anropet placeras.
>
> Om du inte ser något telefon alternativ är det möjligt att din organisation inte tillåter att du använder ett telefonnummer för verifiering. I så fall måste du välja en annan metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Konfigurera telefonsamtal från sidan säkerhets information

Beroende på din organisations inställningar kanske du kan använda telefonsamtal som en av dina säkerhets metoder.

>[!Note]
>Om du vill ta emot ett SMS i stället för ett telefonsamtal följer du stegen i artikeln [Konfigurera säkerhets information för att använda SMS-meddelanden](security-info-setup-text-msg.md) .

### <a name="to-set-up-phone-calls"></a>Konfigurera telefonsamtal

1. Logga in på ditt arbets-eller skol konto och gå sedan till https://myprofile.microsoft.com/ din sida.

    ![Sidan min profil, som visar markerade länkar för säkerhets information](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhets information** i det vänstra navigerings fönstret eller från länken i **säkerhets informations** blocket och välj sedan **Lägg till metod** på sidan **säkerhets information** .

    ![Sidan säkerhets information med markerat Lägg till metod-alternativ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **telefon** i list rutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod, med valt telefonnummer](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Skriv telefonnumret för din mobila enhet på sidan **telefon** och välj **Ring mig**och sedan **Nästa**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Besvara verifierings telefonsamtalet, som skickas till det telefonnummer du angav, och följ anvisningarna.

    Sidan ändras för att visa din framgång.

    ![Meddelande om lyckad kommunikation, anslutning av telefonnumret, valet att ta emot telefonsamtal och ditt konto](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Din säkerhets information uppdateras och du kan använda telefonsamtal för att verifiera din identitet när du använder tvåstegsverifiering eller lösen ords återställning. Om du vill ringa telefonsamtal som standard metod, se avsnittet [ändra din standard säkerhets information](#change-your-default-security-info-method) i den här artikeln.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Ta bort telefonsamtal från dina metoder för säkerhets information

Om du inte längre vill använda telefonsamtal som en metod för säkerhets information kan du ta bort det från sidan **säkerhets information** .

>[!Important]
>Om du tar bort telefonsamtal av misstag finns det inget sätt att ångra det. Du måste lägga till metoden igen enligt stegen i avsnittet [Konfigurera telefonsamtal](#set-up-phone-calls-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-phone-calls"></a>Ta bort telefonsamtal

1. På sidan **säkerhets information** väljer du länken **ta bort** bredvid alternativet **telefon** .

    ![Länk för att ta bort telefon metoden från säkerhets information](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Klicka på **Ja** i bekräftelse rutan för att ta bort **telefonnumret** . När ditt telefonnummer har tagits bort tas det bort från din säkerhets information och försvinner från sidan **säkerhets information** . Om **telefonen** är standard-metoden ändras standard till en annan tillgänglig metod.

## <a name="change-your-default-security-info-method"></a>Ändra din standard metod för säkerhets information

Om du vill att telefonsamtal ska vara standard metoden som används när du loggar in på ditt arbets-eller skol konto med hjälp av tvåstegsverifiering eller för förfrågningar om lösen ords återställning, kan du ställa in det på sidan **säkerhets information** .

### <a name="to-change-your-default-security-info-method"></a>Ändra din standard metod för säkerhets information

1. På sidan **säkerhets information** väljer du länken **ändra** bredvid standard information om **inloggnings metoden** .

    ![Ändra länk för standard inloggnings metod](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Välj **telefon samtal ( *_your_phone_number_* )** i list rutan med tillgängliga metoder och välj sedan **Bekräfta**.

    ![Välj metod för standard inloggning](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    Standard metoden som används för inloggnings ändringar till **telefon-anrop ( *_your_phone_number_* )** .

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhets information

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad som you're försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-app.** Hämta och Använd en Authenticator-app för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännande kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhets information så att en autentiserare används](security-info-setup-auth-app.md).

- **Mobil enhets text.** Ange ditt mobila enhets nummer och få en text kod som du kan använda för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett textmeddelande (SMS) finns i [Konfigurera säkerhets information för att använda SMS (text messaging)](security-info-setup-text-msg.md).

- **Säkerhets nyckel.** Registrera din Microsoft-kompatibla säkerhets nyckel och Använd den tillsammans med en PIN-kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med en säkerhets nyckel finns i [Konfigurera säkerhets information för att använda en säkerhets nyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress till arbetet eller skolan för att få ett e-postmeddelande om lösen ords återställning. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhets information för att använda e-post](security-info-setup-email.md).

- **Säkerhets frågor.** Svara på vissa säkerhets frågor som har skapats av administratören för din organisation. Det här alternativet är bara tillgängligt för lösen ords återställning och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar dina säkerhets frågor finns i artikeln [Konfigurera säkerhets information för att använda säkerhets frågor](security-info-setup-questions.md) .

    >[!Note]
    >Om några av dessa alternativ saknas beror det förmodligen på att din organisation inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
