---
title: Ställ in säkerhetsfrågor som din verifieringsmetod - Azure AD
description: Så här konfigurerar du sidan Säkerhetsinformation (förhandsversion) för att verifiera din identitet med fördefinierade säkerhetsfrågor som verifieringsmetod.
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
ms.openlocfilehash: 236f1e4e41488b926d9fc2e5e717e68090a0ed7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063976"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Ställa in säkerhetsfrågor som din verifieringsmetod

Du kan följa dessa steg för att lägga till din metod för återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till sidan **Säkerhetsinformation** för att lägga till, uppdatera eller ta bort säkerhetsinformationen.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Om du inte ser alternativet säkerhetsfrågor är det möjligt att din organisation inte tillåter att du använder det här alternativet för metoden för återställning av lösenord. I det här fallet måste du välja en annan metod eller kontakta organisationens supportavdelning för mer hjälp.

## <a name="security-verification-versus-password-reset-authentication"></a>Säkerhetsverifiering kontra autentisering om återställning av lösenord

Metoder för säkerhetsinformation används för både tvåfaktorssäkerhetsverifiering och för återställning av lösenord. Dock kan inte alla metoder användas för båda.

| Metod | Används för |
| ------ | -------- |
| Autentiseringsapp | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| SMS | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Telefonsamtal | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Säkerhetsnyckel | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| E-postkonto | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |
| Säkerhetsfrågor | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Konfigurera dina säkerhetsfrågor från sidan Säkerhetsinformation

Beroende på organisationens inställningar kanske du kan välja och svara på några säkerhetsfrågor som en av dina säkerhetsinformationsmetoder. Administratören ställer in antalet säkerhetsfrågor som du måste välja och besvara.

Om du använder säkerhetsfrågor rekommenderar vi att du använder dem tillsammans med en annan metod. Säkerhetsfrågor kan vara mindre säkra än andra metoder eftersom vissa personer kanske känner till svaren på dina frågor.

> [!Note]
> Säkerhetsfrågor lagras privat och säkert på ett användarobjekt i katalogen och kan endast besvaras av dig under registreringen. Det finns inget sätt för administratören att läsa eller ändra dina frågor eller svar.
>
> Om du inte ser alternativet säkerhetsfrågor är det möjligt att din organisation inte tillåter att du använder säkerhetsfrågor för verifiering. Om så är fallet måste du välja en annan metod eller kontakta administratören för mer hjälp.
>
> Administratörskonton får inte använda säkerhetsfrågor som en metod för återställning av lösenord. Om du är inloggad som ett administratörsnivåkonto ser du inte dessa alternativ.

### <a name="to-set-up-your-security-questions"></a>Så här ställer du in dina säkerhetsfrågor

1. Logga in på ditt arbets- eller https://myprofile.microsoft.com/ skolkonto och gå sedan till din sida.

    ![Sidan Min profil, med markerade länkar för säkerhetsinformation](media/security-info/securityinfo-myprofile-page.png)

2. Välj **Säkerhetsinformation** i det vänstra navigeringsfönstret eller från länken i blocket **Säkerhetsinformation** och välj sedan **Lägg till metod** på sidan **Säkerhetsinformation.**

    ![Sidan Säkerhetsinformation med markerat alternativ för Lägg till metod](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **Säkerhetsfrågor** i listrutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod med markerade säkerhetsfrågor](media/security-info/securityinfo-myprofile-addquestions.png)

4. På sidan **Säkerhetsfrågor** väljer och besvarar du dina säkerhetsfrågor och väljer sedan **Spara**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-securityquestions.png)

    Säkerhetsinformationen uppdateras och du kan använda dina säkerhetsfrågor för att verifiera din identitet när du använder återställning av lösenord.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Ta bort säkerhetsfrågor från dina metoder för säkerhetsinformation

Om du inte längre vill använda dina säkerhetsfrågor som en säkerhetsinformationsmetod kan du ta bort dem från sidan **Säkerhetsinformation.**

>[!Important]
>Om du tar bort dina säkerhetsfrågor av misstag kan du inte ångra dem. Du måste lägga till metoden igen om du följer stegen i avsnittet [Konfigurera säkerhetsfrågor](#set-up-your-security-questions-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-your-security-questions"></a>Så här tar du bort dina säkerhetsfrågor

1. På sidan **Säkerhetsinformation** väljer du länken **Ta bort bredvid** alternativet **Säkerhetsfrågor.**

    ![Länk för att ta bort telefonmetoden från säkerhetsinformation](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Välj **Ja** i bekräftelserutan om du vill ta bort dina **säkerhetsfrågor**. När dina säkerhetsfrågor har tagits bort tas metoden bort från din säkerhetsinformation och försvinner från sidan **Säkerhetsinformation.**

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhetsinformation

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad du försöker göra. Följande alternativ är tillgängliga:

- **Appen Autentiserare.** Ladda ned och använd en autentiseringsapp för att få antingen ett godkännandemeddelande eller en slumpmässigt genererad godkännandekod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Text för mobila enheter.** Ange ditt mobilenhetsnummer och få en text som du använder för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett SMS finns i [Konfigurera säkerhetsinformation för att använda SMS](security-info-setup-text-msg.md).

- **Mobil enhet eller arbetssamtal.** Ange ditt mobilenhetsnummer och få ett telefonsamtal för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **Säkerhetsnyckel.** Registrera din Microsoft-kompatibla säkerhetsnyckel och använd den tillsammans med en PIN-kod för tvåstegsverifiering eller återställning av lösenord. Stegvisa instruktioner om hur du verifierar din identitet med en säkerhetsnyckel finns i [Konfigurera säkerhetsinformation för att använda en säkerhetsnyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress för arbete eller skola för att få ett e-postmeddelande om återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

    >[!Note]
    >Om några av dessa alternativ saknas beror det troligen på att organisationen inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
