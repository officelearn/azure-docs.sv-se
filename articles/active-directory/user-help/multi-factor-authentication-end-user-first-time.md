---
title: Vad är sidan Ytterligare verifiering? - Azure AD
description: Så här kommer du till sidan Ytterligare säkerhetsverifiering för tvåfaktorsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 5a7f0e10b23bf1a541fe83c3112962c38f7e1331
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062565"
---
# <a name="what-is-the-additional-verification-page"></a>Vad är sidan Ytterligare verifiering?

Du har fått ett e-postmeddelande från någon på IT-avdelningen eller från din chef om att organisationen har lagt till ytterligare säkerhetsverifiering för ditt konto. Vad betyder det? Det innebär att din organisation vidtar extra åtgärder för att kontrollera att du är den som du uppger dig vara när du loggar in. Den här extra verifieringen, även kallad tvåfaktorsverifiering, görs genom en kombination av ditt användarnamn, ditt lösenord och en mobil enhet eller telefon.

Tvåfaktorsverifiering är säkrare än bara ett lösenord eftersom den är beroende av två former av autentisering: något du vet och något du har med dig. Något du känner till är ditt lösenord. Något du har med dig är en telefon eller enhet som du ofta har med dig. Tvåfaktorsverifiering kan hjälpa till att stoppa illvilliga hackare från att låtsas vara du, för även om de har ditt lösenord är oddsen att de inte har din enhet också.

>[!Important]
>Den här artikeln är avsedd för användare som försöker använda tvåfaktorsverifiering alain@contoso.commed ett arbets- eller skolkonto (till exempel ). Om du är administratör och letar efter information om hur du aktiverar tvåfaktorsverifiering för dina anställda eller andra användare läser du [dokumentationen](https://docs.microsoft.com/azure/active-directory/authentication/)för Azure Active Directory Authentication .

## <a name="who-decides-if-you-use-this-feature"></a>Vem bestämmer om du använder den här funktionen?

Beroende på din typ av konto kan organisationen välja att du måste använda tvåfaktorsverifiering, eller så kanske du kan välja själv.

- **Arbets- eller skolkonto.** Om du använder ett arbets- eller skolkonto (till exempel alain@contoso.com) är det upp till din organisation om du måste använda tvåfaktorsverifiering tillsammans med de specifika verifieringsmetoderna. Eftersom din organisation har beslutat att du måste använda den här funktionen, finns det inget sätt för dig att inaktivera den individuellt.

- **Personligt Microsoft-konto.** Du kan välja att ställa in tvåfaktorsverifiering för dina personliga Microsoft-konton (till exempel alain@outlook.com). Om du har problem med tvåfaktorsverifiering och ditt personliga [Microsoft-konto läser du Aktivera eller inaktivera tvåfaktorsverifiering för ditt Microsoft-konto](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off). Eftersom du väljer om du vill använda den här funktionen kan du aktivera och inaktivera den när du vill.

    >[!Note]
    >Om du har problem med tvåfaktorsverifiering och ett av dina danielle@outlook.compersonliga Microsoft-konton (till exempel) kan du prova förslagen om [hur du använder tvåstegsverifiering med ditt Microsoft-konto](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="access-the-additional-security-verification-page"></a>Få tillgång till sidan Ytterligare säkerhetsverifiering

När din organisation har aktiverats och konfigurerat tvåfaktorsverifiering får du en uppmaning om att ange mer information för att skydda ditt konto.

![Mer info krävs fråga](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Så här öppnar du sidan Ytterligare säkerhetsverifiering

1. Välj **Nästa** i frågan **Mer information krävs.**

    Sidan **Ytterligare säkerhetsverifiering** visas.

2. På sidan **Ytterligare säkerhetsverifiering** måste du bestämma vilken tvåfaktorsverifieringsmetod som ska användas för att verifiera att du är den du säger att du är efter att du har loggat in på ditt arbets- eller skolkonto. Du kan välja:

    | Kontaktmetod | Beskrivning |
    | --- | --- |
    | Mobilapp | <ul><li>**Ta emot meddelanden för verifiering.** Det här alternativet skickar ett meddelande till autentiseringsappen på din smartphone eller surfplatta. Visa meddelandet och välj **Autentisera** i appen om det är berättigat. Ditt arbete eller din skola kan kräva att du anger en PIN-kod innan du autentiserar.</li><li>**Använd verifieringskod.** I det här läget genererar autentiseringsappen en verifieringskod som uppdateras var 30:e sekund. Ange den senaste verifieringskoden på inloggningsskärmen.<br>Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594) och [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Autentisering telefon | <ul><li>**Telefonsamtal placerar** ett automatiskt röstsamtal till det telefonnummer du anger. Besvara samtalet och tryck på pundknappen (#) på telefonens knappsats för att autentisera.</li><li>**Sms** avslutar ett textmeddelande som innehåller en verifieringskod. Efter uppmaningen i texten svarar du antingen på sms:et eller anger verifieringskoden som anges i inloggningsgränssnittet.</li></ul> |
    | Arbetstelefon | Placerar ett automatiskt röstsamtal till det telefonnummer du anger. Besvara samtalet och tryck på pundknappen (#) på telefonens knappsats för att autentisera. |

## <a name="next-steps"></a>Nästa steg

När du har kommit åt sidan **Ytterligare säkerhetsverifiering** måste du välja och ställa in din tvåfaktorsverifieringsmetod:

- [Konfigurera din mobila enhet som verifieringsmetod](multi-factor-authentication-setup-phone-number.md)

- [Konfigurera din kontorstelefon som verifieringsmetod](multi-factor-authentication-setup-office-phone.md)

- [Konfigurera Microsoft Authenticator-appen som verifieringsmetod](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Relaterade resurser

- [Hantera inställningarna för tvåfaktorsverifieringsmetod](multi-factor-authentication-end-user-manage-settings.md)

- [Hantera lösenord för appar](multi-factor-authentication-end-user-app-passwords.md)

- [Logga in med tvåfaktorsverifiering](multi-factor-authentication-end-user-signin.md)

- [Få hjälp med tvåfaktorsverifiering](multi-factor-authentication-end-user-troubleshoot.md) 
