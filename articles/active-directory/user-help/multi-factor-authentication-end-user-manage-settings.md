---
title: Ändra din metod och inställningar för tvåfaktorsverifiering – Azure Active Directory
description: Läs om hur du ändrar metoden för säkerhetsverifiering och inställningarna för ditt arbets- eller skolkonto från sidan Ytterligare säkerhetsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253252"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Ändra din tvåfaktorsverifieringsmetod och inställningar

När du har konfigurerat dina metoder för säkerhetsverifiering för ditt arbets- eller skolkonto kan du uppdatera någon av de relaterade uppgifterna, inklusive:

- Standardmetod för säkerhetsverifiering

- Information om säkerhetsverifieringsmetod, till exempel ditt telefonnummer

- Konfigurera autentiseringsappar eller ta bort en enhet från autentiseringsappen

## <a name="using-the-additional-security-verification-page"></a>Använda sidan Ytterligare säkerhetsverifiering

Om din organisation har gett dig specifika steg om hur du aktiverar och hanterar din tvåfaktorsverifiering bör du följa dessa instruktioner först. Annars kan du komma åt inställningarna för säkerhetsverifieringsmetoden från sidan [Ytterligare säkerhetsverifiering.](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)

>[!Note]
>Om det du ser på skärmen inte stämmer överens med vad som beskrivs i den här artikeln betyder det att antingen administratören har aktiverat upplevelsen **säkerhetsinformation (förhandsversion)** eller att din organisation har tillhandahållit en egen anpassad portal. Mer information om den nya säkerhetsinformationen finns i [Översikt över säkerhetsinformation (förhandsversion).](user-help-security-info-overview.md) Om du vill ha mer information om organisationens anpassade portal måste du kontakta organisationens supportavdelning.

### <a name="to-get-to-the-additional-security-verification-page"></a>Så här kommer du till sidan Ytterligare säkerhetsverifiering

Du kan följa den här länken till [sidan Ytterligare säkerhetsverifiering](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Ytterligare säkerhetsverifieringssida med information om den tillgängliga säkerhetsverifieringsmetoden](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Du kan också komma till sidan **Ytterligare säkerhetsverifiering** genom att följa dessa steg:

1. Logga in [https://myapps.microsoft.com](https://myapps.microsoft.com)på .

1. Välj ditt kontonamn längst upp till höger och välj sedan **profil**.

1. Välj **Ytterligare säkerhetsverifiering**.  

    ![Länken Mina appar länkar till sidan Ytterligare säkerhetsverifiering](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Information om hur du använder avsnittet **Applösenord** på sidan **Ytterligare säkerhetsverifiering** finns i [Hantera applösenord för tvåfaktorsverifiering](multi-factor-authentication-end-user-app-passwords.md). Applösenord bör endast användas för appar som inte stöder tvåfaktorsverifiering.

## <a name="change-your-default-security-verification-method"></a>Ändra standardsäkerhetsverifieringsmetoden

När du har loggat in på ditt arbets- eller skolkonto med ditt användarnamn och lösenord får du automatiskt din valda säkerhetsverifieringsmetod. Beroende på organisationens krav kan detta vara en aviserings- eller verifieringskod via en autentiseringsapp, ett sms eller ett telefonsamtal.

Om du bestämmer dig för att du vill ändra standardsäkerhetsverifieringsmetoden du använder kan du göra det härifrån.

### <a name="to-change-your-default-security-verification-method"></a>Så här ändrar du standardsäkerhetsverifieringsmetoden

1. På sidan **Ytterligare säkerhetsverifiering** väljer du den metod som ska användas i listan **Vad är den önskade alternativlistan.** Du ser alla alternativ, men du kan bara välja de som görs tillgängliga för dig av din organisation.

    - **Meddela mig via app:** Du får ett meddelande via din autentiseringsapp att du har en väntande verifieringsfråga.

    - **Ring min autentiseringstelefon**: Du får ett telefonsamtal på din mobila enhet och ber dig verifiera din information.

    - **Textkod till min autentiseringstelefon**: Du får en verifieringskod som en del av ett textmeddelande på din mobila enhet. Du måste ange den här koden i verifieringsprompten för ditt arbets- eller skolkonto.

    - **Ring min telefon:** Du får ett telefonsamtal på din kontorstelefon och ber dig verifiera din information.

    - **Använd verifieringskod från appen**: Du använder din autentiseringsapp för att få en verifieringskod som du skriver in i prompten från ditt arbets- eller skolkonto.

2. Välj **Spara**.

## <a name="add-or-change-your-phone-number"></a>Lägga till eller ändra ditt telefonnummer

Du kan lägga till nya telefonnummer eller uppdatera befintliga nummer från sidan **Ytterligare säkerhetsverifiering.**

>[!Important]
>Vi rekommenderar starkt att du lägger till ett sekundärt telefonnummer för att förhindra att du blir utelåst från ditt konto om din primära telefon försvinner eller blir stulen, eller om du får en ny telefon och inte längre har ditt ursprungliga, primära telefonnummer.

### <a name="to-change-your-phone-numbers"></a>Så här ändrar du telefonnummer

1. Uppdatera telefonnummerinformationen för **din autentiseringstelefon** (din primära mobila enhet) och **office-telefonen**från avsnittet **Hur vill du svara?** **Additional security verification**

1. Markera rutan bredvid alternativet **Alternativ autentiseringstelefon** och skriv sedan in ett sekundärt telefonnummer där du kan ta emot textmeddelanden eller telefonsamtal om du inte kan komma åt din primära enhet.

1. Välj **Spara**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Lägga till ett nytt konto i Microsoft authenticator-appen

Du kan konfigurera ditt arbets- eller skolkonto i Microsoft Authenticator-appen för [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) eller [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Om du redan har konfigurerat ditt arbets- eller skolkonto i Microsoft Authenticator-appen behöver du inte göra det igen.

1. Välj **Konfigurera Autentiseringsapp**i avsnittet **Additional security verification** **Hur vill du svara?**

    ![Konfigurera ditt arbets- eller skolkonto i Microsoft Authenticator-appen](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Följ anvisningarna på skärmen, inklusive att använda din mobila enhet för att skanna QR-koden, och välj sedan **Nästa**.

    Du blir ombedd att godkänna ett meddelande via Microsoft Authenticator-appen för att verifiera din information.

1. Välj **Spara**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Ta bort ditt konto eller din enhet från Microsoft Authenticator-appen

Du kan ta bort ditt konto från Microsoft Authenticator-appen och du kan ta bort enheten från ditt arbets- eller skolkonto. Vanligtvis tar du bort enheten för att permanent ta bort en förlorad, stulen eller gammal enhet från ditt konto, och du tar bort ditt konto för att försöka åtgärda vissa anslutningsproblem eller åtgärda en kontoändring, till exempel ett nytt användarnamn.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Så här tar du bort enheten från ditt arbets- eller skolkonto

1. Välj knappen **Konfigurera Autentiseringsapp** på **Additional security verification** avsnittet **Hur vill du svara?**

1. Välj **Spara**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Så här tar du bort ditt konto från Microsoft Authenticator-appen

I Microsoft Authenticator-appen väljer du knappen **Ta bort bredvid** den enhet som du vill ta bort.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Aktivera tvåfaktorsverifieringsuppmaningar på en betrodd enhet

Beroende på organisationens inställningar kan du se en **kryssruta** där det står Att inte fråga igen för X dagar när du utför tvåfaktorsverifiering i webbläsaren. Om du har valt det här alternativet för att stoppa tvåfaktorsverifieringsuppmaningar och sedan förlorar enheten eller om enheten eventuellt äventyras, bör du aktivera tvåfaktorsverifieringsanysans igen för att skydda ditt konto. Du måste aktivera anvisningarna för alla dina enheter samtidigt. Tyvärr kan du inte aktivera anvisningarna igen för endast en viss enhet.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Så här aktiverar du tvåfaktorsverifieringsansningar igen för dina enheter

På [sidan **Ytterligare säkerhetsverifiering** ](#to-get-to-the-additional-security-verification-page)väljer du **Återställ multifaktorautentisering på tidigare betrodda enheter**. Nästa gång du loggar in på en enhet uppmanas du att utföra tvåfaktorsverifiering.

## <a name="next-steps"></a>Nästa steg

När du har lagt till eller uppdaterat dina tvåfaktorsverifieringsinställningar kan du hantera applösenorden, logga in eller få hjälp med några vanliga tvåfaktorsverifieringsrelaterade problem.

- [Hantera applösenord för tvåfaktorsverifiering](multi-factor-authentication-end-user-app-passwords.md) för appar som inte stöder tvåfaktorsverifiering.

- [Så här loggar du in med tvåfaktorsverifiering](multi-factor-authentication-end-user-signin.md)

- [Lösa vanliga problem med tvåfaktorsverifiering](multi-factor-authentication-end-user-troubleshoot.md)
