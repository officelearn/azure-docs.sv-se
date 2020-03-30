---
title: Logga in med din information om identitetsautentisering – Azure AD
description: Läs mer om hur du loggar in med de olika metoderna för identitetsverifiering i säkerhetsinformation.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 7d1d27cede6c593150d3937bb3aa0ca59394eea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062174"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Logga in med tvåstegsverifiering eller säkerhetsinformation

När du har konfigurerat tvåstegsverifierings- eller säkerhetsinformation kan du logga in på ditt konto med din angivna verifieringsmetod.

> [!Note]
> Om du fortfarande använder verifieringsupplevelsen i två steg måste du konfigurera verifieringsmetoderna genom att följa instruktionerna i artikeln [Konfigurera mitt konto för tvåstegsverifiering.](multi-factor-authentication-end-user-first-time.md)
>
> Om administratören har aktiverat säkerhetsinformationsupplevelsen måste du ange verifieringsmetoderna med hjälp av följande steg-för-steg-artiklar:<ul><li>[Konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md)</li><li>[Konfigurera säkerhetsinformation för att använda textmeddelanden](security-info-setup-text-msg.md)</li><li>[Konfigurera säkerhetsinformation för att använda ett telefonsamtal](security-info-setup-phone-number.md)</li><li>[Konfigurera säkerhetsinformation för att använda en säkerhetsnyckel](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Logga in med ett meddelande om en autentiseringsapp på din mobila enhet

1. Logga in på ditt konto med ditt användarnamn och lösenord.

2. Välj **Godkänn** från godkännandemeddelandet som skickas till din mobila enhet.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Logga in med en autentiseringsappkod på din mobila enhet

1. Logga in på ditt konto med ditt användarnamn och lösenord.

2. Öppna din autentiseringsapp och skriv den slumpmässigt genererade koden för ditt konto i rutan **Ange kod.**

## <a name="sign-in-using-your-phone-number"></a>Logga in med ditt telefonnummer

1. Logga in på ditt konto med ditt användarnamn och lösenord.

2. Svara i telefonen och följ instruktionerna.

## <a name="sign-in-using-a-text-message"></a>Logga in med ett textmeddelande

1. Logga in på ditt konto med ditt användarnamn och lösenord.

2. Öppna sms:et och skriv koden från sms:et i rutan **Ange kod.**

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Logga in med en säkerhetsnyckel på låsskärmen

1. När du har registrerat säkerhetsnyckeln väljer du säkerhetsnyckelavbildningen på låsskärmen i Windows 10.

2. Sätt i säkerhetsnyckeln i enhetens USB-port och logga in i Windows med hjälp av säkerhetsnyckelns PIN-kod.

    ![Inloggning av säkerhetsnyckel på låsskärmen för Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Logga in med en säkerhetsnyckel och webbläsaren Microsoft Edge

1. När du har registrerat säkerhetsnyckeln öppnar du webbläsaren Microsoft Edge.

2. När du uppmanas att logga in sätter du in säkerhetsnyckeln i enhetens USB-port och loggar in i Windows med hjälp av säkerhetsnyckelns PIN-kod.

    ![Inloggning med säkerhetsnyckel med webbläsaren Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Information om hur du loggar in med appen Microsoft Authenticator finns i artikeln [genom Att logga in på dina konton med hjälp av Microsoft Authenticator-appen](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Logga in med en annan verifieringsmetod

Om du av någon anledning inte kan använda din primära inloggningsmetod kan du använda en annan tidigare konfigurerad verifieringsmetod.

1. Logga in på ditt konto normalt och välj sedan länken Logga in på **ett annat sätt** på sidan **Tvåstegsverifiering.**

    ![Ändra inloggningsverifieringsmetod](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Om du inte ser länken **Logga in på ett annat sätt** betyder det att du inte har konfigurerat några andra verifieringsmetoder och att du måste kontakta administratören för att få hjälp med att logga in på ditt konto. När administratören hjälper dig att logga in kontrollerar du att du lägger till ytterligare verifieringsmetoder. Mer information om hur du lägger till verifieringsmetoder finns i artikeln [Hantera dina inställningar för tvåstegsverifiering.](multi-factor-authentication-end-user-manage-settings.md)
    >
    >Om länken **Logga in på ett annat sätt,** men fortfarande inte ser några andra verifieringsmetoder, måste du kontakta administratören för att få hjälp med att logga in på ditt konto.

2. Välj alternativ verifieringsmetod och fortsätt med tvåstegsverifieringsprocessen.

3. När du är tillbaka i ditt konto kan du uppdatera dina verifieringsmetoder (om det behövs). Mer information om hur du lägger till eller ändrar metoder finns i artikeln [Hantera dina inställningar för tvåstegsverifiering.](multi-factor-authentication-end-user-manage-settings.md)

## <a name="next-steps"></a>Nästa steg

- Läs mer om säkerhetsinformation i översiktsartikeln [säkerhetsinformation (förhandsversion).](user-help-security-info-overview.md)

- Läs mer om tvåstegsverifiering i översiktsartikeln [för tvåstegsverifiering.](user-help-two-step-verification-overview.md)

- Återställa ditt lösenord om du har tappat bort eller glömt det, från [portalen för återställning av lösenord](https://passwordreset.microsoftonline.com/)

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
