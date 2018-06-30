---
title: Azure MFA-inloggning med tvåstegsverifiering | Microsoft Docs
description: Den här sidan ger du riktlinjer för hur du går till olika inloggning tillgängliga metoder visas med Azure MFA.
keywords: autentisering av användare, inloggning, logga in med mobiltelefon, logga in med Arbetstelefon
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: 2cb29d61de43bcd2fc6f9bfb3a94af309b1c3f55
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128487"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Inloggning med Azure Multi-Factor Authentication
> [!NOTE]
> Syftet med den här artikeln är att gå igenom ett typiskt inloggningen. Hjälp med att logga in eller att felsöka problem finns i [har problem med Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Vad är din inloggning?
Din inloggning skiljer sig åt beroende på vad du väljer att använda som din tvåfaktorsautentisering: ett telefonsamtal eller ett authentication-appen texter. Välj det alternativ som bäst beskriver vad du vill göra:

| Hur loggar du in? |
| --- |
| [Med ett telefonsamtal till min mobil eller office telefon](#signing-in-with-a-phone-call) |
| [Med text till min mobiltelefon](#signing-in-with-a-text-message)
| [Med meddelanden från Microsoft Authenticator-appen](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [Med verifieringskoder från Microsoft Authenticator-appen](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [Med en alternativ metod, eftersom det inte kan använda min verifieringsmetod just nu](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logga in med ett telefonsamtal
Följande information beskriver tvåstegsverifiering verifiering upplevelse med ett samtal till din mobil- eller office telefon.

1. Logga in på en applikation eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.  
2. Microsoft anropar du.  
3. Besvara samtalet och tryck på #-knappen.  

## <a name="signing-in-with-a-text-message"></a>Logga in med ett textmeddelande
Följande information beskriver tvåstegsverifiering verifiering upplevelse med ett SMS till din mobiltelefon:

1. Logga in på en applikation eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.
2. Microsoft skickar ett textmeddelande som innehåller ett antal kod.
3. Ange koden i rutan på sidan logga in.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logga in med Microsoft Authenticator-appen
Följande information beskriver användningen av Microsoft Authenticator-appen för tvåstegsverifiering kontroller. Det finns två sätt att använda appen. Du kan ta emot push-meddelanden på enheten eller så kan du öppna appen för att få en Verifieringskod.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Logga in med ett meddelande från Microsoft Authenticator-appen
1. Logga in på en applikation eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.
2. Microsoft skickar ett meddelande till Microsoft Authenticator-appen på enheten.

  ![Microsoft skickar meddelanden](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Öppna meddelandet på din telefon och välj den **Kontrollera** nyckel. Om ditt företag kräver en PIN-kod kan du ange det här.
4. Du bör nu vara inloggad.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Logga in med en Verifieringskod med Microsoft Authenticator-appen

Om du använder Microsoft Authenticator-appen för att hämta verifieringskoder, när du öppnar appen se du ett tal under namnet på ditt konto. Det här talet ändras med 30 sekunders mellanrum så att du inte använder samma nummer två gånger. När du uppmanas för en Verifieringskod öppnar appen och använda tal som visas för närvarande.

1. Logga in på en applikation eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.
2. Microsoft efterfrågar en Verifieringskod.

  ![Ange verifieringskod](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Öppna Microsoft Authenticator-appen på din telefon och ange koden i rutan där du loggar in.

## <a name="signing-in-with-an-alternate-method"></a>Logga in med en alternativ metod
Du saknar ibland telefon eller enhet som du ställer in som din primära verifieringsmetod. Den här situationen är därför rekommenderar vi att du konfigurerar metoder för säkerhetskopiering för ditt konto. I följande avsnitt beskrivs hur du loggar in med en alternativ metod när den primära metoden är inte tillgängliga.

1. Logga in på en applikation eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.
2. Välj **använder ett annat verifieringsalternativ**. Du kan se olika verifieringsalternativ baserat på hur många du har konfigurerat.
3. Välj en alternativ metod och logga in.

  ![Använd alternativ metod](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Nästa steg

Om du har problem med att logga in med tvåstegsverifiering kan få mer information på [har problem med Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

Lär dig hur du [hantera dina inställningar för tvåstegsverifiering](../../../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md).

Lär dig hur du [komma igång med Microsoft Authenticator-appen](../../../../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) så att du kan använda meddelanden för att logga in, i stället för texter och telefonsamtal.
