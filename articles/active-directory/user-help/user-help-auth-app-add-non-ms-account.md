---
title: Lägga till konton som inte kommer från Microsoft i Microsoft Authenticator-appen - Azure AD
description: Lägg till konton som inte kommer från Microsoft, till exempel för Google eller Facebook i Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåfaktorsverifiering.
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
ms.openlocfilehash: 8650d0170e8ff910140e2b432dd1c998d19e72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063959"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Lägga till konton som inte kommer från Microsoft i Microsoft Authenticator-appen

Lägg till dina konton som inte kommer från Microsoft, till exempel för Google, Facebook eller GitHub i Microsoft Authenticator-appen för tvåfaktorsverifiering. Microsoft Authenticator-appen fungerar med alla appar som använder tvåfaktorsverifiering och alla konton som har stöd för tidsbaserade engångslösenord (TOTP).

>[!Important]
>Innan du kan lägga till ditt konto måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu följer du stegen i [hämtnings- och installationsartikeln.](user-help-auth-app-download-install.md)

## <a name="add-personal-accounts"></a>Lägga till personliga konton

I allmänhet måste du för alla dina personliga konton:

1. Logga in på ditt konto och aktivera sedan tvåfaktorsverifiering med enheten eller datorn.

2. Lägg till kontot i Microsoft Authenticator-appen. Du kan bli ombedd att skanna en QR-kod som en del av den här processen.

    >[!Note]
    >Om det är första gången du konfigurerar Microsoft Authenticator-appen kan du få en uppmaning om du vill tillåta appen att komma åt kameran (iOS) eller låta appen ta bilder och spela in video (Android). Du måste välja **Tillåt** så att autentiseringsappen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera autentiseringsappen, men du måste lägga till kodinformationen manuellt. Information om hur du lägger till koden manuellt finns i [Lägg till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

Vi tillhandahåller processen här för dina Facebook-, Google-, GitHub- och Amazon-konton, men den här processen är densamma för alla andra appar, till exempel Instagram, Netflix eller Adobe.

## <a name="add-your-google-account"></a>Lägga till ditt Google-konto

Lägg till ditt Google-konto genom att aktivera tvåfaktorsverifiering och sedan lägga till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsverifiering

1. Gå till https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, välj **Kom igång**och verifiera sedan din identitet på datorn.

2. Följ stegen på sidan för att aktivera tvåstegsverifiering för ditt personliga Google-konto.

### <a name="add-your-google-account-to-the-app"></a>Lägga till ditt Google-konto i appen

1. På Google-sidan på datorn går du till avsnittet **Konfigurera alternativa andra steg** och väljer **Konfigurera** i avsnittet **Autentiseringsapp.**

2. På sidan **Hämta koder från appen Authenticator** väljer du antingen **Android** eller **iPhone** baserat på din telefontyp och väljer sedan **Nästa**.

    Du får en QR-kod som du kan använda för att automatiskt koppla ditt konto till Microsoft Authenticator-appen. Stäng inte det här fönstret.

3. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** från ikonen Anpassa och **kontrollera** längst upp till höger och välj sedan Annat **konto (Google, Facebook osv.)**.

4. Använd enhetens kamera för att skanna QR-koden från sidan **Konfigurera autentiseringshanterare** på datorn.

    >[!Note]
    >Om kameran inte fungerar som den ska kan du ange QR-koden och webbadressen manuellt.

5. Granska sidan **Konton** i Microsoft Authenticator-appen på enheten för att kontrollera att kontoinformationen är rätt och att det finns en tillhörande sexsiffrig verifieringskod.

    För ytterligare säkerhet ändras verifieringskoden var 30:e sekund så att någon inte använder en kod flera gånger.

6. Välj **Nästa** på sidan **Konfigurera autentiseringskontroll** på datorn, skriv den sexsiffriga verifieringskoden som finns i appen för ditt Google-konto och välj sedan **Verifiera**.

7. Ditt konto är verifierat och du kan välja **Klar** för att stänga sidan **Konfigurera autentiseringsautentiseringssida.**

    >[!NOTE]
    >Mer information om tvåfaktorsverifiering och ditt Google-konto finns i [Aktivera tvåstegsverifiering](https://support.google.com/accounts/answer/185839) och [läs mer om tvåstegsverifiering](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Lägg till ditt Facebook-konto

Lägg till ditt Facebook-konto genom att aktivera tvåfaktorsverifiering och sedan lägga till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsverifiering

1. Öppna Facebook på datorn, välj rullgardinsmenyn i det övre högra hörnet och gå sedan till **Inställningar** > **säkerhet och inloggning**.

    Sidan **Säkerhet och inloggning** visas.

2. Gå ned till alternativet **Använd tvåfaktorsautentisering** i avsnittet **Tvåfaktorsautentisering** och välj sedan **Redigera**.

    Sidan **Tvåfaktorsautentisering** visas.

3. Välj **Aktivera**.

### <a name="add-your-facebook-account-to-the-app"></a>Lägga till ditt Facebook-konto i appen

1. På Facebook-sidan på datorn går du till avsnittet **Lägg till en säkerhetskopia** och väljer sedan **Installationsprogrammet** i **området Autentiseringsapp.**

    Du får en QR-kod som du kan använda för att automatiskt koppla ditt konto till Microsoft Authenticator-appen. Stäng inte det här fönstret.

2. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** från ikonen Anpassa och **kontrollera** längst upp till höger och välj sedan Annat **konto (Google, Facebook osv.)**.

3. Använd enhetens kamera för att skanna QR-koden från sidan **Tvåfaktorsautentisering** på datorn.

    >[!Note]
    >Om kameran inte fungerar som den ska kan du ange QR-koden och webbadressen manuellt.

4. Granska sidan **Konton** i Microsoft Authenticator-appen på enheten för att kontrollera att kontoinformationen är rätt och att det finns en tillhörande sexsiffrig verifieringskod.

    För ytterligare säkerhet ändras verifieringskoden var 30:e sekund så att någon inte använder en kod flera gånger.

5. Välj **Nästa** på sidan **Tvåfaktorsautentisering** på datorn och skriv sedan den sexsiffriga verifieringskoden som finns i appen för ditt Facebook-konto.

    Ditt konto är verifierat och du kan nu använda appen för att verifiera ditt konto.

    >[!NOTE]
    >Mer information om tvåfaktorsverifiering och ditt Facebook-konto finns i [Vad är tvåfaktorsautentisering och hur fungerar det?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Lägga till ditt GitHub-konto

Lägg till ditt GitHub-konto genom att aktivera tvåfaktorsverifiering och sedan lägga till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsverifiering

1. Öppna GitHub på datorn, välj bilden från det övre högra hörnet och välj sedan **Inställningar**.

    Sidan **Tvåfaktorsautentisering** visas.

2. Välj **Säkerhet** i **sidofältet Personliga inställningar** och välj sedan Aktivera **tvåfaktorsautentisering** i området **Tvåfaktorsautentisering.**

### <a name="add-your-github-account-to-the-app"></a>Lägga till ditt GitHub-konto i appen

1. På sidan **Tvåfaktorsautentisering** på datorn väljer du **Konfigurera med en app**.

2. Spara dina återställningskoder så att du kan komma tillbaka till ditt konto om du förlorar åtkomst och välj sedan **Nästa**. 

    Du kan spara koderna genom att hämta dem till enheten, skriva ut en papperskopia eller genom att kopiera dem till ett lösenordshanteringsverktyg.

3. På sidan **Autentisering med två faktorer** väljer du **Konfigurera med en app**.

    Sidan ändras för att visa en QR-kod. Stäng inte den här sidan.

4. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** från ikonen Anpassa och **kontrollera** längst upp till höger, välj Annat **konto (Google, Facebook, etc.)** och välj sedan **ange den här textkoden** från texten högst upp på sidan.

    Microsoft Authenticator-appen kan inte skanna QR-koden, så du måste ange koden manuellt.

5. Ange ett **kontonamn** (till exempel GitHub) och skriv **den hemliga nyckeln** från steg 4 och välj sedan **Slutför**.

6. På sidan **Tvåfaktorsautentiseringsberättigare** på datorn skriver du den sexsiffriga verifieringskoden som finns i appen för ditt GitHub-konto och väljer sedan **Aktivera**.

    Sidan **Konton** i appen visar ditt kontonamn och en sexsiffrig verifieringskod. För ytterligare säkerhet ändras verifieringskoden var 30:e sekund så att någon inte använder en kod flera gånger.

    >[!NOTE]
    >Mer information om tvåfaktorsverifiering och ditt GitHub-konto finns i [Om tvåfaktorsautentisering](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Lägg till ditt Amazon-konto

Lägg till ditt Amazon-konto genom att aktivera tvåfaktorsverifiering och sedan lägga till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsverifiering

1. Öppna Amazon på datorn, välj den **nedrullningsbara** menyn Konto & Listor och välj sedan **Ditt konto**.

2. Välj **Logga in & säkerhet,** logga in på ditt Amazon-konto och välj sedan **Redigera** i området **Avancerade säkerhetsinställningar.**

    Sidan **Avancerade säkerhetsinställningar** visas.

3. Välj **Kom igång**.

4. Välj **Autentiseringsapp** på sidan **Välj hur du ska få koder.**

    Sidan ändras för att visa en QR-kod. Stäng inte den här sidan.

5. Öppna Microsoft Authenticator-appen, välj **Lägg till konto** från ikonen Anpassa och **kontrollera** längst upp till höger och välj sedan Annat **konto (Google, Facebook osv.)**.

6. Använd enhetens kamera för att skanna QR-koden från sidan **Välj hur du ska få koder** på datorn.

    >[!Note]
    >Om kameran inte fungerar som den ska kan du ange QR-koden och webbadressen manuellt.

7. Granska sidan **Konton** i Microsoft Authenticator-appen på enheten för att kontrollera att kontoinformationen är rätt och att det finns en tillhörande sexsiffrig verifieringskod.

    För ytterligare säkerhet ändras verifieringskoden var 30:e sekund så att någon inte använder en kod flera gånger.

8. På sidan **Välj hur du ska få koder** på datorn skriver du den sexsiffriga verifieringskoden som finns i appen för ditt Amazon-konto och väljer sedan Verifiera kod och **fortsätt**.

9. Slutför resten av registreringsprocessen, inklusive att lägga till en verifieringsmetod för säkerhetskopiering, till exempel ett textmeddelande, och välj sedan **Skicka kod**.

10. På sidan **Lägg till en verifieringsmetod** för säkerhetskopiering på datorn skriver du den sexsiffriga verifieringskoden som tillhandahålls av din säkerhetskopieringsverifieringsmetod för ditt Amazon-konto och väljer sedan **Verifiera kod och fortsätt**.

11. På sidan **Nästan klar** bestämmer du om du vill göra datorn till en betrodd enhet och väljer sedan **Got it. Aktivera tvåstegsverifiering**.

    Sidan **Avancerade säkerhetsinställningar** visas och visar din uppdaterade information om tvåfaktorsverifiering.

    >[!NOTE]
    >Mer information om tvåfaktorsverifiering och ditt Amazon-konto finns i [Om tvåstegsverifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) och [inloggning med tvåstegsverifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med appen Authenticator på enheten. Mer information finns [i Logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS kan du också säkerhetskopiera dina kontouppgifter och relaterade appinställningar, till exempel ordningen på dina konton, till molnet. Mer information finns i [Säkerhetskopiering och återställning med Microsoft Authenticator-appen](user-help-auth-app-backup-recovery.md).
