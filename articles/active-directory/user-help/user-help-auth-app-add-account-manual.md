---
title: Lägg till ett konto manuellt i appen - Azure Active Directory | Microsoft-dokument
description: Så här lägger du manuellt till dina konton i Microsoft Authenticator-appen för tvåfaktorsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: b237f710866ad3960dd65902c0789228d8c391bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062327"
---
# <a name="manually-add-an-account-to-the-app"></a>Lägg till ett konto manuellt i appen

Om kameran inte kan fånga QR-koden kan du manuellt lägga till kontoinformationen i Microsoft Authenticator-appen för tvåfaktorsverifiering. Detta fungerar för arbets- eller skolkonton och konton som inte kommer från Microsoft.

Koderna för dina konton är inte skiftlägeskänsliga och kräver inte blanksteg när de läggs till i Microsoft Authenticator-appen.

>[!Important]
>Innan du kan lägga till ditt konto måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu följer du stegen i [hämtnings- och installationsartikeln.](user-help-auth-app-download-install.md)

## <a name="add-your-work-or-school-account"></a>Lägga till ditt arbets- eller skolkonto

1. På datorn bör du tänka på informationen **om kod** **och url** på sidan **Konfigurera mobilapp.** Håll den här sidan öppen så att du kan se koden och webbadressen.

    ![Skärm som tillhandahåller QR-koden](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** i ikonen Anpassa och **kontrollera** längst upp till höger och välj sedan **Arbets- eller skolkonto**.

3. Välj **eller ange kod manuellt**.

    ![Skärm för att skanna en QR-kod](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. Ange **koden** och **URL:en** från steg 1 och välj sedan **Slutför**.

    ![Skärm för att ange kod och URL](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    Skärmen **Konton** i appen visar ditt kontonamn och en sexsiffrig verifieringskod. För ytterligare säkerhet ändras verifieringskoden var 30:e sekund så att någon inte använder en kod flera gånger.

## <a name="add-your-google-account"></a>Lägga till ditt Google-konto

1. Välj **KAN INTE SKANNA IT** på sidan Konfigurera **autentiserare** med QR-koden på datorn.

    Sidan **Det går inte att skanna streckkod** visas med den hemliga koden. Håll den här sidan öppen så att du kan se den hemliga koden.

2. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** från ikonen Anpassa och **kontrollera** längst upp till höger, välj Annat **konto (Google, Facebook, etc.)** och välj sedan **ELLER ANGE KOD MANUELLT**.

3. Ange ett **kontonamn** (till exempel Google) och skriv **den hemliga nyckeln** från steg 1 och välj sedan **Slutför**.

4. På sidan **Konfigurera autentiseringskontroll** på datorn skriver du den sexsiffriga verifieringskoden som finns i appen för ditt Google-konto och väljer sedan **Verifiera**.

    Skärmen **Konton** i appen visar ditt kontonamn och en sexsiffrig verifieringskod. För ytterligare säkerhet ändras verifieringskoden var 30:e sekund så att någon inte använder en kod flera gånger.

    >[!NOTE]
    >Mer information om tvåfaktorsverifiering och ditt Google-konto finns i [Aktivera tvåstegsverifiering](https://support.google.com/accounts/answer/185839) och [läs mer om tvåstegsverifiering](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Lägg till ditt Facebook-konto

1. På sidan **Konfigurera via tredje part,** som innehåller QR-koden, och en kod som skrivs ut för inträde i din app. Håll den här sidan öppen så att du kan se koden.

2. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** från ikonen Anpassa och **kontrollera** längst upp till höger, välj Annat **konto (Google, Facebook, etc.)** och välj sedan **ELLER ANGE KOD MANUELLT**.

3. Ange ett **kontonamn** (till exempel Facebook) och skriv **den hemliga nyckeln** från steg 1 och välj sedan **Slutför**.

4. På sidan **Two-Factor Authenticator** på datorn skriver du den sexsiffriga verifieringskoden som finns i appen för ditt Facebook-konto och väljer sedan **Verifiera**.

    Skärmen **Konton** i appen visar ditt kontonamn och en sexsiffrig verifieringskod. För ytterligare säkerhet ändras verifieringskoden var 30:e sekund så att någon inte använder en kod flera gånger.

    >[!NOTE]
    >Mer information om tvåfaktorsverifiering och ditt Facebook-konto finns i [Vad är tvåfaktorsautentisering och hur fungerar det?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-amazon-account"></a>Lägg till ditt Amazon-konto

Du kan lägga till ditt Amazon-konto genom att aktivera tvåfaktorsverifiering och sedan lägga till kontot i appen.

1. Välj **Det går inte att skanna streckkoden** på sidan Välj hur du ska få **koder** med QR-koden.

    **Det går inte att skanna streckkodsmeddelandet** med den hemliga koden. Håll det här meddelandet öppet så att du kan se den hemliga koden.

2. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** från ikonen Anpassa och **kontrollera** längst upp till höger, välj Annat **konto (Google, Facebook, etc.)** och välj sedan **ELLER ANGE KOD MANUELLT**.

3. Ange ett **kontonamn** (till exempel Amazon) och skriv **den hemliga nyckeln** från steg 1 och välj sedan **Slutför**.

4. Slutför resten av registreringsprocessen, inklusive att lägga till en verifieringsmetod för säkerhetskopiering, till exempel ett textmeddelande, och välj sedan **Skicka kod**.

5. På sidan **Lägg till en verifieringsmetod** för säkerhetskopiering på datorn skriver du den sexsiffriga verifieringskoden som tillhandahålls av din säkerhetskopieringsverifieringsmetod för ditt Amazon-konto och väljer sedan **Verifiera kod och fortsätt**.

6. På sidan Nästan klar bestämmer du om du vill göra datorn till en betrodd enhet och väljer sedan **Got it. Aktivera tvåstegsverifiering**.

    Sidan **Avancerade säkerhetsinställningar** visas och visar din uppdaterade information om tvåfaktorsverifiering.

    >[!NOTE]
    >Mer information om tvåfaktorsverifiering och ditt Amazon-konto finns i [Om tvåstegsverifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) och [inloggning med tvåstegsverifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).    

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Appen Microsoft Authenticator på enheten. Mer information finns [i Logga in med appen](user-help-auth-app-sign-in.md).

- Om du har problem med att få din verifieringskod för ditt personliga Microsoft-konto läser du avsnittet **Felsöka verifieringskodproblem** i [säkerhetsinformationen för Microsoft-kontot & verifieringskoder.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- För enheter som kör iOS kan du också säkerhetskopiera dina kontouppgifter och relaterade appinställningar, till exempel ordningen på dina konton, till molnet. Mer information finns i [Säkerhetskopiering och återställning med Microsoft Authenticator-appen](user-help-auth-app-backup-recovery.md).
