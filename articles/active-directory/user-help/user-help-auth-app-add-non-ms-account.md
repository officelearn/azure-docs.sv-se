---
title: Lägg till icke-Microsoft-konton i Microsoft Authenticator-appen – Azure Active Directory | Microsoft Docs
description: Hur du lägger till dina icke-Microsoft-konton, till exempel för Google, Facebook eller GitHub till Microsoft Authenticator-appen för verifiering av två faktorer.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: afcac73af84010f37eccd018f39852712e495b84
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773803"
---
# <a name="add-your-non-microsoft-accounts"></a>Lägg till icke-Microsoft-konton
Lägga till dina icke-Microsoft-konton, till exempel för Google, Facebook och GitHub till Microsoft Authenticator-appen för verifiering av två faktorer. Microsoft Authenticator-appen fungerar med alla appar som använder två faktorer verifiering och ett konto som har stöd för tidsbaserade engångslösenord (TOTP)-standarder.

>[!Important]
>Innan du kan lägga till ditt konto, måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu, följer du stegen i den [ladda ned och installera appen](user-help-auth-app-download-install.md) artikeln.

## <a name="add-personal-accounts"></a>Lägga till personliga konton
För alla dina personliga konton måste du i allmänhet:

1. Logga in på ditt konto och sedan aktivera tvåfaktorsautentisering verifieringen med din enhet eller din dator.

2. Lägga till kontot i Microsoft Authenticator-appen. Du kan bli ombedd att skanna en QR-kod som en del av den här processen.

Vi ger den här processen för dina Facebook, Google, GitHub och Amazon-konton, men den här processen är densamma för alla andra appar, till exempel Instagram, Netflix eller Adobe.

## <a name="add-your-google-account"></a>Lägg till ditt Google-konto
Lägg till ditt Google-konto genom att aktivera tvåfaktorsautentisering verifiering och sedan lägger du till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsautentisering verifiering

1. På din dator går du till https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcomeväljer **börjar**, och verifiera din identitet.

2. Följ stegen på sidan Aktivera tvåstegsverifiering för ditt personliga Google-konto.

### <a name="add-your-google-account-to-the-app"></a>Lägga till ditt Google-konto i appen

1. På sidan Google på din dator går du till den **konfigurera alternativa andra steget** väljer **konfigurera** från den **autentiseringsapp** avsnittet.

2. På den **hämta koder från autentiseringsappen** väljer du antingen **Android** eller **iPhone** baserat på din Telefontyp och välj sedan **nästa**.

    Du får en QR-kod som du kan använda för att automatiskt förknippa ditt konto med Microsoft Authenticator-appen. Stäng inte det här fönstret.

3. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och styra** ikonen längst upp till höger och välj sedan **annat konto (Google, Facebook, etc.)** .

4. Använd enhetens kamera för att skanna QR-kod från den **konfigurera Authenticator** sida på din dator.

    >[!Note]
    >Om din kamera inte fungerar korrekt, kan du [ange QR-koden och Webbadressen manuellt](#add-an-account-to-the-app-manually).

5. Granska den **konton** i Microsoft Authenticator-appen på enheten, för att se till att din kontoinformation är rätt och att det finns en tillhörande sexsiffrig Verifieringskod.

    För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra någon från och med en kod flera gånger.

6. Välj **nästa** på den **konfigurera Authenticator** på din dator, ange den sexsiffriga Verifieringskod som angavs i appen för ditt Google-konto och välj sedan **Kontrollera**.

7. Ditt konto har verifierats och du kan välja **klar** att Stäng den **konfigurera Authenticator** sidan.

    >[!NOTE]
    >Mer information om verifiering av två faktorer och ditt Google-konto finns i [aktivera 2 Autentiseringssteget](https://support.google.com/accounts/answer/185839) och [mer information om 2 Autentiseringssteget](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Lägg till ditt Facebook-konto
Lägg till ditt Facebook-konto genom att aktivera tvåfaktorsautentisering verifiering och sedan lägger du till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsautentisering verifiering

1. På din dator, öppna Facebook, Välj den nedrullningsbara menyn i det övre högra hörnet och sedan gå till **inställningar** > **säkerhets- och logga in**.

    Den **säkerhets- och inloggningen** visas.

2. Gå nedåt till den **Använd tvåfaktorsautentisering** alternativet i den **tvåfaktorautentisering** avsnittet och välj sedan **redigera**.

    Den **tvåfaktorsautentisering** visas.

3. Välj **aktivera**.

### <a name="add-your-facebook-account-to-the-app"></a>Lägga till ditt Facebook-konto i appen

1. På Facebook-sida på din dator går du till den **lägga till en säkerhetskopia** och sedan välja **installationsprogrammet** från den **Authentication-appen** området.

    Du får en QR-kod som du kan använda för att automatiskt förknippa ditt konto med Microsoft Authenticator-appen. Stäng inte det här fönstret.

2. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och styra** ikonen längst upp till höger och välj sedan **annat konto (Google, Facebook, etc.)** .

3. Använd enhetens kamera för att skanna QR-kod från den **två factor-autentisering** sida på din dator.

    >[!Note]
    >Om din kamera inte fungerar korrekt, kan du [ange QR-koden och Webbadressen manuellt](#add-an-account-to-the-app-manually).

4. Granska den **konton** i Microsoft Authenticator-appen på enheten, för att se till att din kontoinformation är rätt och att det finns en tillhörande sexsiffrig Verifieringskod.

    För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra någon från och med en kod flera gånger.

5. Välj **nästa** på den **två factor-autentisering** sidan på datorn och skriv sedan sexsiffrig verifieringskoden som angavs i appen för ditt Facebook-konto.

    Ditt konto har verifierats och du kan nu använda appen för att verifiera ditt konto.

    >[!NOTE]
    >Mer information om verifiering av två faktorer och ditt Facebook-konto finns i [vad är tvåfaktorsautentisering och hur fungerar det?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Lägg till ditt GitHub-konto
Lägg till ditt GitHub-konto genom att aktivera tvåfaktorsautentisering verifiering och sedan lägger du till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsautentisering verifiering

1. På din dator, öppna GitHub, Välj din avbildning i övre högra hörnet och välj sedan **inställningar**.

    Den **tvåfaktorsautentisering** visas.

2. Välj **Security** från den **personliga inställningar** sidopanelen och välj sedan **aktivera tvåfaktorsautentisering** från den **tvåfaktorsautentisering**  området.

### <a name="add-your-github-account-to-the-app"></a>Lägga till ditt GitHub-konto i appen

1. På den **tvåfaktorautentisering** sida på din dator, Välj **skapat med hjälp av en app**.

2. Spara dina recovery-koder så att du kan komma åt ditt konto om du förlorar åtkomst och välj sedan **nästa**. 

    Du kan spara din koder genom att ladda ned dem till din enhet, skriver ut eller kopieras till ett lösenord manager-verktyg.

3. På den **tvåfaktorautentisering** väljer **skapat med hjälp av en app**.

    Sidan ändras så visas en QR-kod. Stäng inte den här sidan.

4. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och styra** ikonen i övre högra, väljer **annat konto (Google, Facebook, etc.)** , och välj sedan **anger den här textkod** från texten längst upp på sidan.

    Microsoft Authenticator-appen kan inte skanna QR-koden så att du måste ange koden manuellt.

5. Ange en **kontonamn** (till exempel GitHub) och Skriv den **hemlig nyckel** från steg 4 och välj sedan **Slutför**.

4. På den **tvåfaktorsautentisering authenticator** på din dator, ange den sexsiffriga Verifieringskod som angavs i appen för ditt GitHub-konto och välj sedan **aktivera**.

    Den **konton** sidan av appen visar namnet på ditt konto och en sexsiffrig Verifieringskod. För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra någon från och med en kod flera gånger.

    >[!NOTE]
    >Mer information om verifiering av två faktorer och ditt GitHub-konto finns i [om tvåfaktorsautentisering](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Lägg till Amazon-konto
Lägg till Amazon-konto genom att aktivera tvåfaktorsautentisering verifiering och sedan lägger du till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåfaktorsautentisering verifiering

1. På din dator, öppna Amazon, Välj den **konto och listor** nedrullningsbara menyn och välj sedan **Your Account**.

2. Välj **inloggning och säkerhet**, logga in på Amazon-konto och välj sedan **redigera** i den **avancerade säkerhetsinställningar** området.

    Den **avancerade säkerhetsinställningar** visas.

3. Välj **börjar**.

4. Välj **Autentiseringsapp** från den **Välj hur du kommer att få koder** sidan.

    Sidan ändras så visas en QR-kod. Stäng inte den här sidan.

5. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och styra** ikonen längst upp till höger och välj sedan **annat konto (Google, Facebook, etc.)** .

6. Använd enhetens kamera för att skanna QR-kod från den **Välj hur du kommer att få koder** sida på din dator.

    >[!Note]
    >Om din kamera inte fungerar korrekt, kan du [ange QR-koden och Webbadressen manuellt](#add-an-account-to-the-app-manually).

5. Granska den **konton** i Microsoft Authenticator-appen på enheten, för att se till att din kontoinformation är rätt och att det finns en tillhörande sexsiffrig Verifieringskod.

    För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra någon från och med en kod flera gånger.

6. På den **Välj hur du kommer att få koder** på din dator, Skriv sexsiffrig verifieringskoden som angavs i appen för Amazon-konto och välj sedan **verifiera kod och fortsätta**.

7. Slutför resten av registreringen, inklusive att lägga till en säkerhetskopiering verifieringsmetod, till exempel ett SMS och välj sedan **skicka kod**.

8. På den **lägga till en säkerhetskopiering verifieringsmetod** på din dator, Skriv sexsiffrig verifieringskoden som tillhandahålls av din säkerhetskopiering verifieringsmetod för Amazon-konto och välj sedan **verifiera kod och fortsätta**.

9. På Almost sidan, bestämma om du vill göra din dator till en betrodd enhet och välj sedan **fattar. Aktivera tvåstegsverifiering**.

    Den **avancerade säkerhetsinställningar** visas, som visar information om din uppdaterade tvåfaktors-verifiering.

    >[!NOTE]
    >Mer information om verifiering av två faktorer och Amazon-konto finns i [om Tvåstegsverifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) och [logga in med Tvåstegsverifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).


## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton till appen, kan du logga in med Authenticator-appen på din enhet. Mer information finns i [logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS, du kan även säkerhetskopiera dina autentiseringsuppgifter och appinställningar, till exempel ordningen på dina konton, som rör molnet. Mer information finns i [säkerhetskopiering och återställning med Microsoft Authenticator-appen](user-help-auth-app-backup-recovery.md).
