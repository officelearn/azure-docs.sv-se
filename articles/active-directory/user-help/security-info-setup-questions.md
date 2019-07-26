---
title: Konfigurera säkerhets information (för hands version) för att använda dina säkerhets frågor – Azure Active Directory | Microsoft Docs
description: Konfigurera din säkerhets information för att verifiera din identitet med hjälp av fördefinierade säkerhets frågor.
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
ms.openlocfilehash: f1c375b64d93662ec50923078549c4f2153fba0a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382823"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Konfigurera säkerhets information (för hands version) för att använda säkerhets frågor

Du kan följa de här stegen för att lägga till din metod för lösen ords återställning. När du har konfigurerat den första gången kan du gå tillbaka till sidan **säkerhets** information för att lägga till, uppdatera eller ta bort din säkerhets information.

När du har ställt in din metod för lösen ords återställning måste du också ställa in en tvåfaktorautentisering med hjälp av en [Authenticator-app](security-info-setup-auth-app.md), [SMS](security-info-setup-text-msg.md)eller ett [telefonsamtal](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Konfigurera dina säkerhets frågor från sidan säkerhets information

Beroende på din organisations inställningar kanske du kan välja och svara på några säkerhets frågor som en av dina säkerhets metoder. Administratören ställer in antalet säkerhets frågor som du måste välja och svara på.

Om du använder säkerhets frågor rekommenderar vi att du använder dem tillsammans med en annan metod. Säkerhets frågor kan vara mindre säkra än andra metoder eftersom vissa personer kanske känner till svaren på dina frågor.

> [!Note]
> Säkerhets frågor lagras privat och säkert på ett användar objekt i katalogen och kan bara besvaras av dig under registreringen. Det finns inget sätt för administratören att läsa eller ändra dina frågor eller svar.
>
> Om du inte ser alternativet säkerhets frågor är det möjligt att din organisation inte tillåter att du använder säkerhets frågor för verifiering. Om så är fallet måste du välja en annan metod eller kontakta administratören om du vill ha mer hjälp.
>
> Administratörs konton får inte använda säkerhets frågor som en metod för lösen ords återställning. Om du är inloggad som ett konto på administratörs nivå visas inte dessa alternativ.

### <a name="to-set-up-your-security-questions"></a>Konfigurera dina säkerhets frågor

1. Logga in på ditt arbets-eller skol konto och gå sedan till https://myprofile.microsoft.com/ din sida.

    ![Sidan min profil, som visar markerade länkar för säkerhets information](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhets information** i det vänstra navigerings fönstret eller från länken i **säkerhets informations** blocket och välj sedan **Lägg till metod** på sidan **säkerhets information** .

    ![Sidan säkerhets information med markerat Lägg till metod-alternativ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **säkerhets frågor** i list rutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod med säkerhets frågor valda](media/security-info/securityinfo-myprofile-addquestions.png)

4. På sidan **säkerhets frågor** väljer du och svarar på dina säkerhets frågor och väljer sedan **Spara**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-securityquestions.png)

    Din säkerhets information uppdateras och du kan använda dina säkerhets frågor för att verifiera din identitet när du använder lösen ords återställning.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Ta bort säkerhets frågor från dina metoder för säkerhets information

Om du inte längre vill använda dina säkerhets frågor som säkerhets information kan du ta bort dem från sidan **säkerhets information** .

>[!Important]
>Om du tar bort dina säkerhets frågor av misstag finns det inget sätt att ångra det. Du måste lägga till metoden igen enligt stegen i avsnittet [Konfigurera dina säkerhets frågor](#set-up-your-security-questions-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-your-security-questions"></a>Ta bort dina säkerhets frågor

1. På sidan **säkerhets information** väljer du länken **ta bort** bredvid alternativet **säkerhets frågor** .

    ![Länk för att ta bort telefon metoden från säkerhets information](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Välj **Ja** i bekräftelse rutan om du vill ta bort dina **säkerhets frågor**. När dina säkerhets frågor har tagits bort tas metoden bort från din säkerhets information och försvinner från sidan **säkerhets information** .

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhets information

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad som you're försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-app.** Hämta och Använd en Authenticator-app för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännande kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhets information så att en autentiserare används](security-info-setup-auth-app.md).

- **Mobil enhets text.** Ange ditt mobila enhets nummer och få en text kod som du kan använda för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett textmeddelande (SMS) finns i [Konfigurera säkerhets information för att använda SMS (text messaging)](security-info-setup-text-msg.md).

- **Mobil enhets-eller arbets telefon samtal.** Ange ditt mobila enhets nummer och få ett telefonsamtal för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhets information så att telefonsamtal används](security-info-setup-phone-number.md).

- **Säkerhets nyckel.** Registrera din Microsoft-kompatibla säkerhets nyckel och Använd den tillsammans med en PIN-kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med en säkerhets nyckel finns i [Konfigurera säkerhets information för att använda en säkerhets nyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress till arbetet eller skolan för att få ett e-postmeddelande om lösen ords återställning. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhets information för att använda e-post](security-info-setup-email.md).

    >[!Note]
    >Om några av dessa alternativ saknas beror det förmodligen på att din organisation inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
