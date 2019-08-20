---
title: Konfigurera en översikt över metoder för verifiering av två faktorer – Azure Active Directory | Microsoft Docs
description: Översikt över hur du ställer in dina metoder för tvåstegsverifiering för tvåstegsverifiering.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616179"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>Konfigurera en översikt över metoder för verifiering av två faktorer

Din organisation har aktiverat tvåstegsverifiering, vilket innebär att ditt arbets-eller skol konto loggar nu kräver en kombination av ditt användar namn, ditt lösen ord och en mobil enhet eller telefon. Din organisation aktiverade den här extra verifieringen eftersom det är säkrare än bara ett lösen ord, som förlitar sig på två typer av autentisering: något du känner och något som du har med dig. Tvåstegsverifiering kan hjälpa till att förhindra att skadliga hackare är du, eftersom även om de har ditt lösen ord, strider är att de inte har din enhet.

>[!Important]
>Innehållet är avsett för användare. Om du är administratör hittar du mer information om hur du konfigurerar och hanterar en Azure Active Directory (Azure AD)-miljö i [dokumentationen om Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="who-decides-if-you-use-this-feature"></a>Vem bestämmer om du använder den här funktionen?

Beroende på din typ av konto kan organisationen välja att du måste använda tvåfaktorsverifiering, eller så kanske du kan välja själv.

- **Arbets- eller skolkonto.** Om du använder ett arbets- eller skolkonto (till exempel alain@contoso.com) är det upp till din organisation om du måste använda tvåfaktorsverifiering tillsammans med de specifika verifieringsmetoderna. Eftersom din organisation har valt att du måste använda den här funktionen kan du inaktivera den på ett och samma sätt.

- **Personligt Microsoft-konto.** Du kan välja att ställa in tvåfaktorsverifiering för dina personliga Microsoft-konton (till exempel alain@outlook.com). Om du har problem med tvåstegsverifiering och din personliga Microsoft-konto kan du läsa mer i [Aktivera och inaktivera tvåstegsverifiering för din Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Eftersom du väljer om du vill använda den här funktionen kan du aktivera och inaktivera den när du vill.

## <a name="access-the-additional-security-verification-page"></a>Öppna sidan ytterligare säkerhets verifiering

När din organisation har aktiverat och ställt in tvåstegsverifiering, får du en uppmaning som uppmanar dig att ange mer information som hjälper dig att skydda ditt konto.

![Prompt för mer information krävs](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Så här öppnar du sidan ytterligare säkerhets verifiering

1. Välj **Nästa** från frågan **Mer information krävs** .

    Sidan **ytterligare säkerhets verifiering** visas.

2. På sidan **ytterligare säkerhets verifiering** måste du bestämma vilken metod för tvåstegsverifiering som ska användas för att kontrol lera att du har loggat in på ditt arbets-eller skol konto. Du kan välja:

    | Kontaktmetod | Beskrivning |
    | --- | --- |
    | Mobilapp | <ul><li>**Ta emot meddelanden om verifiering.** Med det här alternativet skickas ett meddelande till Authenticator-appen på din smartphone eller surfplatta. Visa meddelandet och välj **autentisera** i appen om det är giltigt. Ditt arbete eller din skola kan kräva att du anger en PIN-kod innan du autentiserar.</li><li>**Använd verifierings kod.** I det här läget genererar Authenticator-appen en verifierings kod som uppdateras var 30: e sekund. Ange den mest aktuella verifierings koden på inloggnings skärmen.<br>Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594) och [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefon för autentisering | <ul><li>**Telefonsamtal** placerar ett automatiskt röst samtal till det telefonnummer som du anger. Besvara anropet och tryck på fyrkant (#) på telefon knappen för att autentisera.</li><li>**SMS** avslutar ett textmeddelande som innehåller en verifierings kod. Följ anvisningarna i texten, antingen svara på textmeddelandet eller ange verifierings koden som anges i inloggnings gränssnittet.</li></ul> |
    | Arbetstelefon | Placerar ett automatiskt röst samtal till det telefonnummer som du anger. Besvara anropet och tryck på fyrkant (#) på telefon knappen för att autentisera. |

## <a name="next-steps"></a>Nästa steg

När du har öppnat sidan **ytterligare säkerhets verifiering** måste du välja och konfigurera en metod för verifiering av två faktorer:

- [Konfigurera din mobila enhet som din verifierings metod](multi-factor-authentication-setup-phone-number.md)

- [Konfigurera din arbets telefon som din verifierings metod](multi-factor-authentication-setup-office-phone.md)

- [Konfigurera Microsoft Authenticator-appen som din verifierings metod](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Relaterade resurser

- [Hantera dina inställningar för verifierings metoden med två faktorer](multi-factor-authentication-end-user-manage-settings.md)

- [Hantera lösenord för appar](multi-factor-authentication-end-user-app-passwords.md)

- [Logga in med tvåstegsverifiering](multi-factor-authentication-end-user-signin.md)

- [Få hjälp med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md) 
