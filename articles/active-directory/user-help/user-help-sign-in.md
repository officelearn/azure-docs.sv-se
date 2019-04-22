---
title: Logga in med tvåstegsverifiering verifiering eller din säkerhetsinformation - Azure Active Directory | Microsoft Docs
description: Läs mer om hur du loggar in med olika identitet verifieringsmetoderna säkerhetsinformation.
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
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60e4d9a4cb555dfbc9f05961487a8f794d19e1bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268368"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Logga in med tvåstegsverifiering verifiering eller din säkerhetsinformation
När du har konfigurerat tvåstegsverifiering eller säkerhetsinformation kommer du att kunna logga in på ditt konto med angivna autentiseringsmetod.

> [!Note]
> Om du fortfarande använder verifieringsupplevelse tvåstegsverifiering, du måste du konfigurera dina autentiseringsmetoder genom att följa anvisningarna i den [konfigurerar mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md) artikeln.
> 
> Om din administratör har aktiverat info säkerhetsupplevelse, måste du ange dina autentiseringsmetoder med hjälp av dessa steg för steg-artiklar:<ul><li>[Ställa in säkerhetsinformation för att använda en authentication-appen](security-info-setup-auth-app.md)</li><li>[Konfigurera säkerhetsinformation för att använda textmeddelanden](security-info-setup-text-msg.md)</li><li>[Konfigurera säkerhetsinformation för att använda ett telefonsamtal](security-info-setup-phone-number.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Logga in med en avisering för authenticator-appen på din mobila enhet

1. Logga in på ditt konto med ditt användarnamn och lösenord.

2. Välj **Godkänn** från meddelande om godkännande skickas till din mobila enhet.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Logga in med en kod för authenticator-appen på din mobila enhet

1. Logga in på ditt konto med ditt användarnamn och lösenord.

2. Öppna autentiseringsappen och ange slumpmässigt genererat kod för ditt konto i den **ange koden** box.

## <a name="sign-in-using-your-phone-number"></a>Logga in med ditt telefonnummer

1. Logga in på ditt konto med ditt användarnamn och lösenord.

2. Besvara din telefon och följ instruktionerna.

## <a name="sign-in-using-a-text-message"></a>Logga in med ett textmeddelande

1. Logga in på ditt konto med ditt användarnamn och lösenord.

2. Öppna textmeddelandet och ange koden från din textmeddelande till den **ange koden** box.

## <a name="sign-in-using-another-verification-method"></a>Logga in med en annan verifieringsmetod
Om det inte går att använda din primära inloggningsmetod av någon anledning, kan du använda en annan tidigare har konfigurerat verifieringsmetod.

1. Logga in på ditt konto normalt och välj sedan den **loggar du in ett annat sätt** länkar på den **tvåstegsverifiering** sidan.

    ![Ändra inloggning verifieringsmetod](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Om du inte ser den **loggar du in ett annat sätt** länken, betyder det att du inte har konfigurerat några andra verifieringsmetoder och att du måste kontakta administratören för hjälp med att logga in på ditt konto. När administratören hjälper dig att logga in, kontrollera att du lägger till ytterligare verifieringsmetoder. Mer information om att lägga till verifieringsmetoder finns i den [hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md) artikeln.
    > 
    >Om du ser den **loggar du in ett annat sätt** koppla, men fortfarande inte ser några andra verifieringsmetoder, måste du kontakta administratören för hjälp med att logga in på ditt konto.

2. Välj din alternativa verifieringsmetod och fortsätta med tvåstegsprocessen för verifiering.

3. När du är tillbaka i ditt konto kan du uppdatera din verifieringsmetoder (vid behov). Mer information om Lägg till eller ändra din metod, se den [hantera dina inställningar för tvåstegsverifiering](multi-factor-authentication-end-user-manage-settings.md) artikeln.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om säkerhetsinformation i den [info (förhandsversion) Säkerhetsöversikt](user-help-security-info-overview.md) artikeln.

- Lär dig mer om tvåstegsverifiering i den [tvåstegsverifiering verifiering översikt](user-help-two-step-verification-overview.md) artikeln. 

- Återställa lösenordet om du har förlorat den, från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
