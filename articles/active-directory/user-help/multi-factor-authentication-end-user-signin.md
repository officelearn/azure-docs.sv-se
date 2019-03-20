---
title: Azure MFA-inloggningen med tvåstegsverifiering - Azure Active Directory | Microsoft Docs
description: Den här sidan får du vägledning om var du kan se olika inloggning metoder med Azure MFA.
keywords: autentisering av användare, inloggning, logga in med mobiltelefon, logga in med alternativet för Arbetstelefon
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1350b2d86e18f213d99f1c27d64e371451f5f9b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840890"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Inloggning med Azure Multi-Factor Authentication
> [!NOTE]
> Syftet med den här artikeln är att gå igenom ett typiskt inloggningen. Hjälp med att logga in eller att felsöka problem finns i [har problem med Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

## <a name="what-will-your-sign-in-experience-be"></a>Vad blir din inloggning?
Din inloggning skiljer sig beroende på vad du väljer att använda som dina andra faktor: ett telefonsamtal, ett authentication-appen eller texter. Välj det alternativ som bäst beskriver vad du vill göra:

| Hur loggar du in? |
| --- |
| [Med ett telefonsamtal till min mobil- eller office-telefon](#signing-in-with-a-phone-call) |
| [Med ett meddelande till min mobiltelefon](#signing-in-with-a-text-message)
| [Med aviseringar från Microsoft Authenticator-appen](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Med verifieringskoder från Microsoft Authenticator-appen |
| [Med en alternativ metod, eftersom jag inte kan använda mitt bästa metoden just nu](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Logga in med ett telefonsamtal
Följande information beskriver tvåstegsverifiering verifieringsupplevelse med ett anrop till telefonen mobil- eller office.

1. Logga in på ett program eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.  
2. Microsoft anropar du.  
3. Besvara samtalet och tryck på #-knappen.  

## <a name="signing-in-with-a-text-message"></a>Logga in med ett textmeddelande
Följande information beskriver tvåstegsverifiering verifieringsupplevelse med ett SMS till din mobiltelefon:

1. Logga in på ett program eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.
2. Microsoft skickar ett textmeddelande som innehåller ett antal koden.
3. Ange koden i rutan på sidan logga in.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Logga in med Microsoft Authenticator-appen
Följande information beskriver användningen av Microsoft Authenticator-appen för verifiering i två steg. Det finns två olika sätt att använda appen. Du kan ta emot push-meddelanden på enheten eller du kan öppna appen för att få en Verifieringskod.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Logga in med ett meddelande från Microsoft Authenticator-appen
1. Logga in på ett program eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.
2. Microsoft skickar ett meddelande till Microsoft Authenticator-appen på din enhet.

   ![Microsoft skickar meddelande](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Öppna meddelandet på din telefon och välj den **Kontrollera** nyckel. Om ditt företag kräver en PIN-kod kan du ange den här.
4. Du bör nu loggat in.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Logga in med en Verifieringskod med Microsoft Authenticator-appen

Om du använder Microsoft Authenticator-appen för att hämta verifieringskoder, sedan visas när du öppnar appen ett tal under kontonamnet på ditt. Det här talet ändras med 30 sekunders mellanrum så att du inte använder samma nummer två gånger. När du blir tillfrågad om en Verifieringskod, öppna appen och Använd tal som visas för närvarande.

1. Logga in på ett program eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.
2. Microsoft uppmanar dig en Verifieringskod.

   ![Ange verifieringskod](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Öppna Microsoft Authenticator-appen på telefonen och ange koden i rutan där du loggar in.

## <a name="signing-in-with-an-alternate-method"></a>Logga in med en alternativ metod
Du behöver ibland telefon eller enhet som du konfigurerar som din prioriterad verifieringsmetod. Den här situationen är därför som vi rekommenderar att du konfigurerar metoder för säkerhetskopiering för ditt konto. Följande avsnitt visar hur du loggar in med en alternativ metod när din primära metoden är inte kanske tillgänglig.

1. Logga in på ett program eller tjänst, till exempel Office 365 med ditt användarnamn och lösenord.
2. Välj **använder ett annat verifieringsalternativ**. Du kan se olika verifieringsalternativ baserat på hur många du har konfigurerat.
3. Välj en alternativ metod och logga in.

   ![Använd alternativ metod](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Nästa steg
- Om du har problem med att logga in med tvåstegsverifiering kan få mer information på [har problem med Azure Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

- Lär dig hur du [hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md).

- Lär dig hur du [Kom igång med Microsoft Authenticator-appen](user-help-auth-app-download-install.md) så att du kan använda meddelanden för att logga in, i stället för texter och telefonsamtal.
