---
title: Konfigurera en mobil enhet som din tvåfaktorsverifieringsmetod - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du konfigurerar en mobil enhet som din tvåfaktorsverifieringsmetod.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: b0bfaa87c77ba9fff9f6605c1989e48ffbc3fb35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062514"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Konfigurera en mobil enhet som din tvåfaktorsverifieringsmetod

Du kan ställa in din mobila enhet så att den fungerar som din tvåfaktorsverifieringsmetod. Din mobiltelefon kan antingen ta emot ett sms med en verifieringskod eller ett telefonsamtal.

>[!Note]
> Om alternativet autentiseringstelefon är nedtonat är det möjligt att din organisation inte tillåter att du använder ett telefonnummer eller sms för verifiering. I det här fallet måste du välja en annan metod eller kontakta administratören för mer hjälp.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Konfigurera din mobila enhet så att den använder ett textmeddelande som verifieringsmetod

1. På sidan **Ytterligare säkerhetsverifiering** väljer du **Autentiseringstelefon** i **steg 1: Hur ska vi kontakta dig,** välja ditt land eller din region i listrutan och skriv sedan in mobiltelefonnumret för din mobila enhet.

2. Välj **Skicka mig en kod via sms** från området **Metod** och välj sedan **Nästa**.

    ![Ytterligare säkerhetsverifieringssida med autentiseringstelefon och sms](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Skriv verifieringskoden från sms:et som skickas från Microsoft till **steg 2: Vi har skickat ett sms till telefonområdet** och välj sedan **Verifiera**.

    ![Ytterligare säkerhetsverifieringssida med autentiseringstelefon och sms](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. Från **steg 3: Fortsätt att använda ditt befintliga programområde** kopierar du det medföljande applösenordet och klistrar in det på ett säkert ställe.

    ![Området Applösenord på sidan Ytterligare säkerhetsverifiering](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Information om hur du använder applösenordet med dina äldre appar finns i [Hantera applösenord](multi-factor-authentication-end-user-app-passwords.md). Du behöver bara använda applösenord om du fortsätter att använda äldre appar som inte stöder tvåfaktorsverifiering.

5. Välj **Done** (Klar).

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Konfigurera din mobila enhet för att ta emot ett telefonsamtal

1. På sidan **Ytterligare säkerhetsverifiering** väljer du **Autentiseringstelefon** i **steg 1: Hur ska vi kontakta dig,** välja ditt land eller din region i listrutan och skriv sedan in mobiltelefonnumret för din mobila enhet.

2. Välj **Ring mig** i området **Metod** och välj sedan **Nästa**.

    ![Ytterligare säkerhetsverifieringssida med autentiseringstelefon och telefonsamtal](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Du får ett telefonsamtal från Microsoft och ber dig trycka på bulttecknet (#) på din mobila enhet för att verifiera din identitet.

    ![Testa det angivna telefonnumret](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. Från **steg 3: Fortsätt att använda ditt befintliga programområde** kopierar du det medföljande applösenordet och klistrar in det på ett säkert ställe.

    ![Området Applösenord på sidan Ytterligare säkerhetsverifiering](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Information om hur du använder applösenordet med dina äldre appar finns i [Hantera applösenord](multi-factor-authentication-end-user-app-passwords.md). Du behöver bara använda applösenord om du fortsätter att använda äldre appar som inte stöder tvåfaktorsverifiering.

5. Välj **Done** (Klar).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din tvåfaktorsverifieringsmetod kan du lägga till ytterligare metoder, hantera inställningar och applösenord, logga in eller få hjälp med några vanliga tvåfaktorsverifieringsrelaterade problem.

- [Hantera inställningarna för tvåfaktorsverifieringsmetod](multi-factor-authentication-end-user-manage-settings.md)

- [Hantera lösenord för appar](multi-factor-authentication-end-user-app-passwords.md)

- [Logga in med tvåfaktorsverifiering](multi-factor-authentication-end-user-signin.md)

- [Få hjälp med tvåfaktorsverifiering](multi-factor-authentication-end-user-troubleshoot.md)
