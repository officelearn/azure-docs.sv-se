---
title: Konfigurera en e-postadress som din verifieringsmetod - Azure AD
description: Så här konfigurerar du sidan Säkerhetsinformation (förhandsversion) för att verifiera din identitet med hjälp av en e-postadress som verifieringsmetod.
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
ms.openlocfilehash: 9488b96dda15b0ffb794f83826dc299a5f397b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064010"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Konfigurera en e-postadress som verifieringsmetod

Du kan följa dessa steg för att lägga till din metod för återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till sidan **Säkerhetsinformation** för att lägga till, uppdatera eller ta bort säkerhetsinformationen.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Om du inte ser något e-postalternativ är det möjligt att din organisation inte tillåter att du använder det här alternativet för metoden för återställning av lösenord. I det här fallet måste du välja en annan metod eller kontakta organisationens supportavdelning för mer hjälp.

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

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Konfigurera din e-postadress från sidan Säkerhetsinformation

Beroende på organisationens inställningar kanske du kan använda din e-postadress som en av dina metoder för säkerhetsinformation.

>[!Note]
>Vi rekommenderar att du använder en e-postadress som inte kräver nätverkslösenord för att komma åt. Om du inte ser e-postalternativet är det möjligt att din organisation inte tillåter att du använder ett e-postmeddelande för verifiering. Om så är fallet måste du välja en annan metod eller kontakta administratören för mer hjälp.

### <a name="to-set-up-your-email-address"></a>Så här konfigurerar du din e-postadress

1. Logga in på ditt arbets- eller https://myprofile.microsoft.com/ skolkonto och gå sedan till din sida.

    ![Sidan Min profil, med markerade länkar för säkerhetsinformation](media/security-info/securityinfo-myprofile-page.png)

2. Välj **Säkerhetsinformation** i det vänstra navigeringsfönstret eller från länken i blocket **Säkerhetsinformation** och välj sedan **Lägg till metod** på sidan **Säkerhetsinformation.**

    ![Sidan Säkerhetsinformation med markerat alternativ för Lägg till metod](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **E-post** i listrutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod, med e-post markerat](media/security-info/securityinfo-myprofile-addemail.png)

4. Skriv din e-postadress (till exempel alain@gmail.com) på sidan **E-post** och välj sedan **Nästa**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Den här e-postadressen kan inte vara din e-postadress för arbete eller skola.

5. Skriv koden som skickas till den angivna e-postadressen och välj sedan **Nästa**.

    ![Lägga till telefonnummer och välj textmeddelanden](media/security-info/securityinfo-myprofile-emailcode.png)

    Säkerhetsinformationen uppdateras och du kan använda din e-postadress för att verifiera din identitet när du använder återställning av lösenord.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Ta bort din e-postadress från dina metoder för säkerhetsinformation

Om du inte längre vill använda din e-postadress som en säkerhetsinformationsmetod kan du ta bort den från sidan **Säkerhetsinformation.**

>[!Important]
>Om du tar bort din e-postadress av misstag kan du inte ångra den. Du måste lägga till metoden igen, i och med stegen i avsnittet [Konfigurera din e-postadress](#set-up-your-email-address-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-your-email-address"></a>Så här tar du bort din e-postadress

1. På sidan **Säkerhetsinformation** väljer du länken **Ta bort** bredvid alternativet **E-post.**

    ![Länk för att ta bort telefonmetoden från säkerhetsinformation](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Välj **Ja** i bekräftelserutan om du vill ta bort **e-postkontot.** När e-postkontot har tagits bort tas det bort från din säkerhetsinformation och försvinner från sidan **Säkerhetsinformation.**

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhetsinformation

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad du försöker göra. Följande alternativ är tillgängliga:

- **Appen Autentiserare.** Ladda ned och använd en autentiseringsapp för att få antingen ett godkännandemeddelande eller en slumpmässigt genererad godkännandekod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Text för mobila enheter.** Ange ditt mobilenhetsnummer och få en text som du använder för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett SMS finns i [Konfigurera säkerhetsinformation för att använda SMS](security-info-setup-text-msg.md).

- **Mobil enhet eller arbetssamtal.** Ange ditt mobilenhetsnummer och få ett telefonsamtal för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **Säkerhetsnyckel.** Registrera din Microsoft-kompatibla säkerhetsnyckel och använd den tillsammans med en PIN-kod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med en säkerhetsnyckel finns i [Konfigurera säkerhetsinformation för att använda en säkerhetsnyckel](security-info-setup-security-key.md).

- **Säkerhetsfrågor.** Svara på några säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgängligt för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i artikeln [Konfigurera säkerhetsinformation för att använda säkerhetsfrågor.](security-info-setup-questions.md)

    >[!Note]
    >Om några av dessa alternativ saknas beror det troligen på att organisationen inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
