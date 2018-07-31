---
title: Konfigurera säkerhetsinformation för att använda e-post – Azure Active Directory | Microsoft Docs
description: Ställ in din säkerhetsinformation för att verifiera din identitet med hjälp av företagets eller skolans e-postadress.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: aa575340c0775369d4be05b5e323c05f7dd3218a
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348555"
---
# <a name="set-up-security-info-to-use-email-preview"></a>Konfigurera säkerhetsinformation för att använda e-post (förhandsversion)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Konfigurera din säkerhetsinformation måste du logga in på ditt arbets- eller skolkonto konto och sedan slutföra registreringen. Om du aldrig har konfigurerat din säkerhetsinformation kommer du bli ombedd att göra det nu.

## <a name="set-up-email"></a>Konfigurera e-post

Beroende på organisationens inställningar kan uppmanas du att lägga till en e-postadress till din säkerhetsinformation när du loggar in. I annat fall för att börja konfigurera e-post i säkerhetsinformation, följer du stegen i [hantera din säkerhetsinformation](security-info-manage-settings.md).

>[!Note]
>Om du inte ser alternativet e-post, är det möjligt att din organisation tillåter inte att du använder ett e-postmeddelande för verifiering. Om så är fallet, måste du välja en annan metod eller kontakta administratören för mer hjälp.

### <a name="to-use-your-email-address"></a>Att använda din e-postadress

1. Välj den **e-post** alternativet och Skriv din e-postadress i rutan. E-postadressen får inte vara din arbets- eller skolkonto e-post.

     ![Säkerhetssidan information med e-post](media/security-info/security-info-keep-secure-setup-email.png)

2. Sök efter ett e-postmeddelande från Microsoft för din organisation, ange verifieringskoden som ingår i den **verifiera din e-** och väljer sedan **klar**.

     ![Säkerhetssidan information med inmatningsfält för e-verifiering kod](media/security-info/security-info-verify-email.png)

    >[!Note]
    >Om du inte ser ett e-postmeddelande från Microsoft för din organisations räkning, kontrollera att du har skrivit din e-postadress korrekt och kontrollera mapparna skräppost eller skräppost.

3. I den **skydda ditt konto** väljer **klar**.

    Din säkerhetsinformation uppdateras om du vill använda din e-postadress för att verifiera din identitet när du använder tvåstegsverifiering.

## <a name="additional-security-info-options"></a>Ytterligare säkerhetsalternativ för information

Du har alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-appen.** Ladda ned och använda en autentiseringsapp för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännandekod för återställning av tvåstegsverifiering verifiering eller lösenord. Stegvisa instruktioner om hur du skapar och använder Microsoft Authenticator-appen finns i [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Text för mobila enheter.** Ange enhetsnumret till din mobila och få ett textmeddelande när en kod som du använder för tvåstegsverifiering eller lösenord återställa. Stegvisa instruktioner om hur du verifierar din identitet med hjälp av ett textmeddelande (SMS) finns i [konfigurera säkerhetsinformation för att använda textmeddelanden (SMS)](security-info-setup-text-msg.md).

- **Mobil enhet eller arbete telefonsamtal.** Ange enhetsnumret till din mobila och få ett telefonsamtal för verifiering eller lösenord återställning av två steg. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **Säkerhetsfrågor.** Svara på vissa säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgänglig för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i den [konfigurera säkerhetsinformation kan använda säkerhetsfrågor](security-info-setup-questions.md) artikeln.
    
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Om du behöver uppdatera din säkerhetsinformation, följ instruktionerna i den [hantera din säkerhetsinformation](security-info-manage-settings.md) artikeln.

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följer du stegen i den [återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md) artikeln.

- Hämta felsöka tips och hjälp med inloggningsproblem i den [kan inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.