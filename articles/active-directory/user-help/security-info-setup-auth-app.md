---
title: Konfigurera säkerhetsinformation för att använda en autentiseringsapp - Azure Active Directory | Microsoft Docs
description: Konfigurera din säkerhetsinformation för att verifiera din identitet med hjälp av Microsoft Authenticator-appen.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 6ee75b0ee465e330d78b166b0da12232b7915ae7
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766069"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>Konfigurera säkerhetsinformation för att använda en autentiseringsapp (förhandsversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Konfigurera din säkerhetsinformation måste du logga in på ditt arbets- eller skolkonto konto och sedan slutföra registreringen. Om du aldrig har konfigurerat din säkerhetsinformation kommer du bli ombedd att göra det nu.

## <a name="set-up-the-microsoft-authenticator-app"></a>Konfigurera Microsoft Authenticator-appen

Beroende på organisationens inställningar kan uppmanas du att konfigurera Microsoft Authenticator-appen när du loggar in. I annat fall för att börja konfigurera Microsoft Authenticator-appen säkerhetsinformation, följer du stegen i [hantera din säkerhetsinformation](security-info-manage-settings.md).

Om du vill hämta och Läs mer om Microsoft Authenticator-appen, se [Kom igång med Microsoft Authenticator-appen](user-help-auth-app-download-install.md).

>[!Note]
>Om du inte vill använda Microsoft Authenticator-appen, kan du välja en annan app under Konfigurera. Den här artikeln använder Microsoft Authenticator-appen. Om du inte ser authenticator-appalternativet, är det möjligt att din organisation tillåter inte att du använder en authentication-appen för att bekräfta. Om så är fallet, måste du välja en annan metod eller kontakta administratören för mer hjälp.

### <a name="to-use-the-microsoft-authenticator-app"></a>Att använda Microsoft Authenticator-appen

1. Välj den **autentiseringsapp** alternativet.

    Den **hämta appen** guiden visas.

    ![Hämta app-guiden, första skärmen](media/security-info/security-info-auth-app-wizard.png)

    Om du inte vill använda Microsoft Authenticator-appen, kan du klicka på den **jag vill använda en annan autentiseringsapp** länka från den **hämta appen** skärmen.

2. När du har installerat Microsoft Authenticator-appen, Välj **nästa**.

    Om du uppmanas att meddelanden, lägga till ett nytt konto och välj sedan **arbets- eller skolkonto**.

3. Välj **Nästa**.

    Den **skanna QR-koden** skärmen visas.

    ![Skanna QR-kod med hjälp av Authenticator-appen](media/security-info/security-info-scan-qr.png)

4. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och kontroll** ikonen i det övre högra hörnet, och välj sedan **arbets- eller skolkonto**. 

5. Om du har en QR-kod reader-appen kan du skanna koden som tillhandahålls. Om du inte har en kod reader-appen kan du välja den **kan inte skanna QR-kod länken** och manuellt ange koden och Webbadressen till Microsoft Authenticator-appen.

6. Använd Microsoft Authenticator-appen om du vill godkänna meddelandet för att aktivera appen.

    Din säkerhetsinformation uppdateras om du vill använda Microsoft Authenticator-appen för att verifiera din identitet när du använder två verifieringen eller Självbetjäning för återställning av lösenord.

    >[!Note]
    >Om din organisation tillåter det, får du även en Verifieringskod tillsammans med avisering för Microsoft Authenticator-appen. Om du vill göra koden din standardmetod, följer du anvisningarna i [hantera din säkerhetsinformation](security-info-setup-auth-app.md).

## <a name="additional-security-info-options"></a>Ytterligare säkerhetsalternativ för information

Du har ytterligare alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Text för mobila enheter.** Ange enhetsnumret till din mobila och få ett textmeddelande när en kod som du använder för tvåstegsverifiering eller lösenord återställa. Stegvisa instruktioner om hur du verifierar din identitet med hjälp av ett textmeddelande (SMS) finns i [konfigurera säkerhetsinformation för att använda textmeddelanden (SMS)](security-info-setup-text-msg.md).

- **Mobil enhet eller arbete telefonsamtal.** Ange enhetsnumret till din mobila och få ett telefonsamtal för verifiering eller lösenord återställning av två steg. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **E-postadress.** Ange företagets eller skolans e-postadress för att få ett e-postmeddelande för återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in din e-post finns i [konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).

- **Säkerhetsfrågor.** Svara på vissa säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgänglig för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i den [konfigurera säkerhetsinformation kan använda säkerhetsfrågor](security-info-setup-questions.md) artikeln.
    
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Om du behöver uppdatera din säkerhetsinformation, följ instruktionerna i den [hantera din säkerhetsinformation](security-info-manage-settings.md) artikeln.

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följer du stegen i den [återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md) artikeln.

- Hämta felsöka tips och hjälp med inloggningsproblem i den [kan inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.
