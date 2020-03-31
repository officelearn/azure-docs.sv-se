---
title: Konfigurera en autentiseringsapp som din tvåfaktorsverifieringsmetod - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du konfigurerar Microsoft Authenticator-appen som din tvåfaktorsverifieringsmetod.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 6ab1f7c97173021cc112a5f117469abd74ac954d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062582"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Konfigurera en autentiseringsapp som din tvåfaktorsverifieringsmetod

Du kan konfigurera en autentiseringsapp för att skicka ett meddelande till din mobila enhet eller skicka en verifieringskod som din säkerhetsverifieringsmetod. Du behöver inte använda Microsoft Authenticator-appen och du kan välja en annan app under installationen. I den här artikeln används dock Microsoft Authenticator-appen.

>[!Important]
>Innan du kan lägga till ditt konto måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu följer du stegen i [hämtnings- och installationsartikeln.](user-help-auth-app-download-install.md)

>[!Note]
> Om alternativet Mobilapp är nedtonat är det möjligt att din organisation inte tillåter att du använder en autentiseringsapp för verifiering. I det här fallet måste du välja en annan metod eller kontakta administratören för mer hjälp.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Konfigurera Microsoft Authenticator-appen för att skicka meddelanden

1. På sidan **Ytterligare säkerhetsverifiering** väljer du **Mobilapp** i **steg 1: Hur ska vi kontakta dig.**

2. Välj **Ta emot meddelanden för verifiering** från området Hur vill du använda området för **mobilapp** och välj sedan **Konfigurera**.

    ![Ytterligare säkerhetsverifieringssida med mobilapp och meddelandealternativ](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Sidan **Konfigurera mobilapp** visas.

    ![Skärm som tillhandahåller QR-koden](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** i ikonen Anpassa och **kontrollera** längst upp till höger och välj sedan **Arbets- eller skolkonto**.

    >[!Note]
    >Om det är första gången du konfigurerar Microsoft Authenticator-appen kan du få en uppmaning om du vill tillåta appen att komma åt kameran (iOS) eller låta appen ta bilder och spela in video (Android). Du måste välja **Tillåt** så att autentiseringsappen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera autentiseringsappen, men du måste lägga till kodinformationen manuellt. Information om hur du lägger till koden manuellt finns i [Lägg till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

4. Använd enhetens kamera för att skanna QR-koden från skärmen **Konfigurera mobilapp** på datorn och välj sedan **Nästa**.

5. Gå tillbaka till datorn och sidan **Ytterligare säkerhetsverifiering,** se till att du får meddelandet som säger att konfigurationen lyckades och välj sedan **Nästa**.

    ![Ytterligare säkerhetsverifieringssida med meddelande om lyckad](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    Autentiseringsappen skickar ett meddelande till din mobila enhet som ett test.

6. På din mobila enhet väljer du **Godkänn**.

7. Lägg till mobiltelefonnumret för din mobila enhet i **steg 3: Om du förlorar åtkomsten till mobilappsområdet** och sedan väljer **Nästa**.

    Vi rekommenderar starkt att du lägger till ditt mobiltelefonnummer för att fungera som en säkerhetskopia om du av någon anledning inte kan komma åt eller använda mobilappen.

8. Från **steg 4: Fortsätt att använda ditt befintliga programområde** kopierar du det medföljande applösenordet och klistrar in det på ett säkert ställe.

    ![Området Applösenord på sidan Ytterligare säkerhetsverifiering](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Information om hur du använder applösenordet med dina äldre appar finns i [Hantera applösenord](multi-factor-authentication-end-user-app-passwords.md). Du behöver bara använda applösenord om du fortsätter att använda äldre appar som inte stöder tvåfaktorsverifiering.

9. Välj **Done** (Klar).

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Konfigurera Microsoft Authenticator-appen så att verifieringskoder används

1. På sidan **Ytterligare säkerhetsverifiering** väljer du **Mobilapp** i **steg 1: Hur ska vi kontakta dig.**

2. Välj **Använd verifieringskod** från **området Hur vill du använda området för mobilapp** och välj sedan **Konfigurera**.

    ![Ytterligare säkerhetsverifieringssida med mobilapp och meddelandealternativ](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Sidan **Konfigurera mobilapp** visas.

    ![Skärm som tillhandahåller QR-koden](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** i ikonen Anpassa och **kontrollera** längst upp till höger och välj sedan **Arbets- eller skolkonto**.

    >[!Note]
    >Om det är första gången du konfigurerar Microsoft Authenticator-appen kan du få en uppmaning om du vill tillåta appen att komma åt kameran (iOS) eller låta appen ta bilder och spela in video (Android). Du måste välja **Tillåt** så att autentiseringsappen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera autentiseringsappen, men du måste lägga till kodinformationen manuellt. Information om hur du lägger till koden manuellt finns i [Lägg till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

4. Använd enhetens kamera för att skanna QR-koden från skärmen **Konfigurera mobilapp** på datorn och välj sedan **Nästa**.

5. Gå tillbaka till datorn och sidan **Ytterligare säkerhetsverifiering,** se till att du får meddelandet som säger att konfigurationen lyckades och välj sedan **Nästa**.

    ![Ytterligare säkerhetsverifieringssida med meddelande om lyckad](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    Autentiseringsappen ber om en verifieringskod som ett test.

6. Bläddra ned till ditt arbets- eller skolkonto i Appen Microsoft Authenticator, kopiera och klistra in den sexsiffriga koden från appen i **steg 2: Ange verifieringskoden från mobilappsrutan** på datorn och välj sedan **Verifiera**.

    ![Ytterligare säkerhetsverifieringssida med test av verifieringskod](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. Lägg till mobiltelefonnumret för din mobila enhet i **steg 3: Om du förlorar åtkomsten till mobilappsområdet** och sedan väljer **Nästa**.

    Vi rekommenderar starkt att du lägger till ditt mobiltelefonnummer för att fungera som en säkerhetskopia om du av någon anledning inte kan komma åt eller använda mobilappen.

8. Från **steg 4: Fortsätt att använda ditt befintliga programområde** kopierar du det medföljande applösenordet och klistrar in det på ett säkert ställe.

    ![Området Applösenord på sidan Ytterligare säkerhetsverifiering](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Information om hur du använder applösenordet med dina äldre appar finns i [Hantera applösenord](multi-factor-authentication-end-user-app-passwords.md). Du behöver bara använda applösenord om du fortsätter att använda äldre appar som inte stöder tvåfaktorsverifiering.

9. Välj **Done** (Klar).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din tvåfaktorsverifieringsmetod kan du lägga till ytterligare metoder, hantera inställningar och applösenord, logga in eller få hjälp med några vanliga tvåfaktorsverifieringsrelaterade problem.

- [Hantera inställningarna för tvåfaktorsverifieringsmetod](multi-factor-authentication-end-user-manage-settings.md)

- [Hantera lösenord för appar](multi-factor-authentication-end-user-app-passwords.md)

- [Logga in med tvåfaktorsverifiering](multi-factor-authentication-end-user-signin.md)

- [Få hjälp med tvåfaktorsverifiering](multi-factor-authentication-end-user-troubleshoot.md)
