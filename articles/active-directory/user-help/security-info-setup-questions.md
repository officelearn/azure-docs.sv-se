---
title: Konfigurera säkerhetsinformation (förhandsversion) för att använda dina säkerhetsfrågor - Azure Active Directory | Microsoft Docs
description: Hur du ställer in din säkerhetsinformation för att verifiera din identitet med hjälp av fördefinierade säkerhetsfrågor.
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
ms.openlocfilehash: 3e5d1546c658631911f25c43e94275f00c7a5140
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60474639"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Konfigurera säkerhetsinformation (förhandsversion) kan använda säkerhetsfrågor
Du kan följa stegen nedan för att lägga till metoden för återställning av lösenord. När du har konfigurerat detta första gången kan du gå tillbaka till den **säkerhetsinformation** sidan för att lägga till, uppdatera eller ta bort din säkerhetsinformation.

När du har konfigurerat metoden för återställning av lösenord, du måste också ställa in din tvåfaktorsautentisering verifieringsmetod, med hjälp av en [autentiseringsapp](security-info-setup-auth-app.md), [textmeddelanden](security-info-setup-text-msg.md), eller en [telefonsamtal](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Konfigurera dina säkerhetsfrågor från sidan säkerhetsinformation
Beroende på inställningarna för din organisation kanske du kan välja och svara på några säkerhetsfrågor som en av din information säkerhetsmetoder. Administratören anger antalet säkerhetsfrågor som du blir ombedd att välja och svara på.

Om du använder säkerhetsfrågor, bör du använda dem tillsammans med en annan metod. Säkerhetsfrågor kan vara mindre säkert än andra metoder, eftersom vissa kanske vet svaren på dina frågor.

> [!Note]
> Säkerhetsfrågor lagras säkert och privat på ett användarobjekt i katalogen och kan endast besvaras av dig under registreringen. Det går inte för administratören att läsa eller ändra din frågorna och svaren.
> 
> Om du inte ser säkerhetsalternativ för frågor, är det möjligt att din organisation tillåter inte att du kan använda säkerhetsfrågor för verifiering. Om så är fallet, måste du välja en annan metod eller kontakta administratören för mer hjälp.
> 
> Administratörskonton får inte använda säkerhetsfrågor som en metod för lösenordsåterställning. Om du är inloggad som ett administratörskonto för nivån visas inte dessa alternativ.

### <a name="to-set-up-your-security-questions"></a>Du ställer in dina säkerhetsfrågor

1. Logga in på ditt arbets- eller skolkonto konto och gå sedan till din https://myprofile.microsoft.com/ sidan.

    ![Min profil-sidan som visar markerad Security info länkar](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhetsinformation** från det vänstra navigeringsfönstret eller från länken i den **säkerhetsinformation** blockera och sedan välja **tilläggsmetod** från den **säkerhetsinformation**  sidan.

    ![Sidan för Security information med markerade Lägg till metodalternativet](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På den **Lägg till en metod** väljer **säkerhetsfrågor** från listrutan och välj sedan **Lägg till**.

    ![Lägg till metoden rutan med säkerhetsfrågor valda](media/security-info/securityinfo-myprofile-addquestions.png)

4. På den **säkerhetsfrågor** sidan, välja och besvara dina säkerhetsfrågor och välj sedan **spara**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-securityquestions.png)

    Din säkerhetsinformation uppdateras och du kan använda dina säkerhetsfrågor för att verifiera din identitet när du använder återställning av lösenord.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Ta bort säkerhetsfrågor från din info säkerhetsmetoder
Om du inte längre vill använda dina säkerhetsfrågor som en säkerhetsmetod information du kan ta bort dem från den **säkerhetsinformation** sidan.

>[!Important]
>Om du tar bort dina säkerhetsfrågor av misstag, går det inte att ångra åtgärden. Du måste lägga till metoden igen, följa stegen i den [ställa in dina säkerhetsfrågor](#set-up-your-security-questions-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-your-security-questions"></a>Att ta bort dina säkerhetsfrågor

1. På den **säkerhetsinformation** väljer den **ta bort** länka bredvid den **säkerhetsfrågor** alternativet.

    ![Länk för att ta bort metoden phone från säkerhetsinformation](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Välj **Ja** från dialogrutan Bekräfta att ta bort din **säkerhetsfrågor**. När dina säkerhetsfrågor har tagits bort, metoden tas bort från din säkerhetsinformation och försvinner den från den **säkerhetsinformation** sidan.

## <a name="additional-security-info-methods"></a>Ytterligare information om säkerhetsmetoder
Du har ytterligare alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-appen.** Ladda ned och använda en autentiseringsapp för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännandekod för återställning av tvåstegsverifiering verifiering eller lösenord. Stegvisa instruktioner om hur du skapar och använder Microsoft Authenticator-appen finns i [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Text för mobila enheter.** Ange enhetsnumret till din mobila och få ett textmeddelande när en kod som du använder för tvåstegsverifiering eller lösenord återställa. Stegvisa instruktioner om hur du verifierar din identitet med hjälp av ett textmeddelande (SMS) finns i [konfigurera säkerhetsinformation för att använda textmeddelanden (SMS)](security-info-setup-text-msg.md).

- **Mobil enhet eller arbete telefonsamtal.** Ange enhetsnumret till din mobila och få ett telefonsamtal för verifiering eller lösenord återställning av två steg. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **E-postadress.** Ange företagets eller skolans e-postadress för att få ett e-postmeddelande för återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in din e-post finns i [konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).
   
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
