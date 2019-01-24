---
title: Konfigurera säkerhetsinformation kan använda säkerhetsfrågor - Azure Active Directory | Microsoft Docs
description: Konfigurera din säkerhetsinformation för att verifiera din identitet med hjälp av fördefinierade säkerhetsfrågor.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 0ee486c41193608887763b611ed2a879cb5e0ef4
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814791"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>Konfigurera säkerhetsinformation kan använda fördefinierade säkerhetsfrågor (förhandsversion)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Konfigurera din säkerhetsinformation måste du logga in på ditt arbets- eller skolkonto konto och sedan slutföra registreringen. Om du aldrig har konfigurerat din säkerhetsinformation kommer du bli ombedd att göra det nu.

## <a name="set-up-security-questions"></a>Konfigurera säkerhetsfrågor

Beroende på organisationens inställningar kan uppmanas du att lägga till säkerhetsfrågor till din säkerhetsinformation när du loggar in. I annat fall för att börja konfigurera säkerhetsfrågor säkerhetsinformation, följer du stegen i [hantera din säkerhetsinformation](security-info-manage-settings.md).

Om du använder säkerhetsfrågor, bör du använda dem tillsammans med en annan metod. Säkerhetsfrågor kan vara mindre säkert än andra metoder, eftersom vissa kanske känner till svar på en annan person frågor.

>[!Note]
>Säkerhetsfrågor lagras säkert och privat på ett användarobjekt i katalogen och kan endast besvaras av dig under registreringen. Det går inte för administratören att läsa eller ändra din frågorna och svaren.<br>Om du inte ser säkerhetsalternativ för frågor, är det möjligt att din organisation tillåter inte att du kan använda säkerhetsfrågor för verifiering. Om så är fallet, måste du välja en annan metod eller kontakta administratören för mer hjälp.

### <a name="to-choose-and-answer-your-security-questions"></a>Du väljer och besvara dina säkerhetsfrågor

1. Välj **säkerhetsfrågor**, och välj sedan vilka säkerhetsfrågor som du vill svara på. 

    Antalet säkerhetsfrågor som man måste avgörs av din administratör.

    ![Säkerhetsinformation sidan, välja dina säkerhetsfrågor](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. Ange svar för dina valda frågor och välj sedan **klar**.

## <a name="additional-security-info-options"></a>Ytterligare säkerhetsalternativ för information

Du har alternativ för hur din organisation kontakter du verifiera din identitet, baserat på vad är du försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-appen.** Ladda ned och använda en autentiseringsapp för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännandekod för återställning av tvåstegsverifiering verifiering eller lösenord. Stegvisa instruktioner om hur du skapar och använder Microsoft Authenticator-appen finns i [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md).

- **Text för mobila enheter.** Ange enhetsnumret till din mobila och få ett textmeddelande när en kod som du använder för tvåstegsverifiering eller lösenord återställa. Stegvisa instruktioner om hur du verifierar din identitet med hjälp av ett textmeddelande (SMS) finns i [konfigurera säkerhetsinformation för att använda textmeddelanden (SMS)](security-info-setup-text-msg.md).

- **Mobil enhet eller arbete telefonsamtal.** Ange enhetsnumret till din mobila och få ett telefonsamtal för verifiering eller lösenord återställning av två steg. Stegvisa instruktioner om hur du verifierar din identitet med ett telefonnummer finns i [konfigurera säkerhetsinformation för att använda telefonsamtal](security-info-setup-phone-number.md).

- **E-postadress.** Ange företagets eller skolans e-postadress för att få ett e-postmeddelande för återställning av lösenord. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du ställer in din e-post finns i [konfigurera säkerhetsinformation för att använda e-post](security-info-setup-email.md).
   
    >[!Note]
    >Om vissa av dessa alternativ saknas, är det mest sannolika eftersom din organisation inte tillåter dessa metoder. Om så är fallet, måste du välja en tillgänglig metod eller kontakta administratören för mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Om du behöver uppdatera din säkerhetsinformation, följ instruktionerna i den [hantera din säkerhetsinformation](security-info-manage-settings.md) artikeln.

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följer du stegen i den [återställa lösenordet för arbets- eller skolkonto](user-help-reset-password.md) artikeln.

- Hämta felsöka tips och hjälp med inloggningsproblem i den [kan inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.