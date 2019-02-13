---
title: Konfigurera din säkerhetsinformation för att använda textmeddelanden - Azure Active Directory | Microsoft Docs
description: Konfigurera din säkerhetsinformation för att verifiera din identitet med hjälp av ett textmeddelande (SMS).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9556c99e7e9655054ee5b834db36ba6e22e9e807
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182664"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>Konfigurera säkerhetsinformation för att använda textmeddelanden (förhandsversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Konfigurera din säkerhetsinformation måste du logga in på ditt arbets- eller skolkonto konto och sedan slutföra registreringen. Om du aldrig har konfigurerat din säkerhetsinformation kommer du bli ombedd att göra det nu.

## <a name="set-up-text-messaging"></a>Konfigurera textmeddelanden

Beroende på organisationens inställningar kan uppmanas du att lägga till textmeddelanden till din säkerhetsinformation när du loggar in. I annat fall för att börja konfigurera textmeddelanden säkerhetsinformation, följer du stegen i [hantera din säkerhetsinformation](security-info-manage-settings.md).

Alternativet text är en del av Telefonalternativet, så att du kan ange allt in på samma sätt som du skulle för ditt telefonnummer, men i stället för att låta Microsoft ringer upp dig, du väljer att använda ett textmeddelande. Om du inte ser Telefonalternativet, är det möjligt att din organisation tillåter inte att du använder ett telefonnummer för att bekräfta. Om så är fallet, måste du välja en annan metod eller kontakta administratören för mer hjälp.

### <a name="to-use-a-text-message"></a>Du använder ett textmeddelande

1. Välj den **Phone** alternativet.

    Den **ställa in din telefon** guiden visas.

    ![Konfigurera ditt land eller region och telefonnumret nummer](media/security-info/security-info-keep-secure-setup-text.png)

2. Välj din **land eller Region** från listrutan, ange ditt telefonnummer (inklusive riktnumret, om tillämpligt) i den **telefonnummer** väljer den **SMS: a mig en kod** och välj sedan **nästa**.

    Du får ett SMS med en kod som du måste ange på en sida för verifiering.

    ![Verifiering där du kan ange text Meddelandekod](media/security-info/security-info-keep-secure-verify-text-msg.png)

    Din säkerhetsinformation uppdateras för att skicka ett textmeddelande för att verifiera din identitet när du använder två verifieringen eller Självbetjäning för återställning av lösenord.

    >[!Note]
    >Om du vill få ett telefonsamtal i stället för ett textmeddelande, följer du stegen i den [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md) artikeln.

## <a name="additional-security-info-options"></a>Ytterligare säkerhetsalternativ för information

Du har alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-appen.** Ladda ned och använda en autentiseringsapp för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännandekod för återställning av tvåstegsverifiering verifiering eller lösenord. Stegvisa instruktioner om hur du skapar och använder Microsoft Authenticator-appen finns i [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Mobil enhet eller arbete telefonsamtal.** Ange enhetsnumret till din mobila och få ett telefonsamtal för verifiering eller lösenord återställning av två steg. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **E-postadress.** Ange företagets eller skolans e-postadress för att få ett e-postmeddelande för återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in din e-post finns i [konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).
   
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

- **Säkerhetsfrågor.** Svara på vissa säkerhetsfrågor som skapats av administratören för din organisation. Det här alternativet är endast tillgänglig för återställning av lösenord och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in dina säkerhetsfrågor finns i den [konfigurera säkerhetsinformation kan använda säkerhetsfrågor](security-info-setup-questions.md) artikeln.

## <a name="next-steps"></a>Nästa steg

- Om du behöver uppdatera din säkerhetsinformation, följ instruktionerna i den [hantera din säkerhetsinformation](security-info-manage-settings.md) artikeln.

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följer du stegen i den [återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md) artikeln.

- Hämta felsöka tips och hjälp med inloggningsproblem i den [kan inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.
