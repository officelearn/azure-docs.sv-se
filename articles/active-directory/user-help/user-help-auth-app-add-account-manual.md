---
title: Manuellt lägga till ett konto i appen – Azure Active Directory | Microsoft Docs
description: Så här att manuellt lägga till dina konton i Microsoft Authenticator-appen för verifiering av två faktorer.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e82e8f6caafb38b758f552913def234a92a0d83
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454658"
---
# <a name="manually-add-an-account-to-the-app"></a>Manuellt lägga till ett konto i appen
Om din kamera inte att samla in QR-koden kan du manuellt lägga till din kontoinformation till Microsoft Authenticator-appen för verifiering av två faktorer. Detta fungerar för arbets- eller skolkonton och icke-Microsoft-konton.

Koder för dina konton är inte skiftlägeskänsliga och kräver inte blanksteg när läggs till i Microsoft Authenticator-appen.

>[!Important]
>Innan du kan lägga till ditt konto, måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu, följer du stegen i den [ladda ned och installera appen](user-help-auth-app-download-install.md) artikeln.

## <a name="add-your-work-or-school-account"></a>Lägg till ditt arbets- eller skolkonto

1. På din dator, Observera de **kod** och **Url** information om den **konfigurera mobilappen** sidan. Behåll den här sidan öppen så att du kan se koden och Webbadressen.
    
    ![Skärmen som tillhandahåller QR-kod](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och kontroll** ikonen i det övre högra hörnet, och välj sedan **arbets- eller skolkonto**.

3. Välj **eller ange koden MANUELLT**.

    ![Skärmen för att skanna en QR-kod](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)
   
4. Ange den **kod** och **URL** från steg 1 och välj sedan **Slutför**.

    ![Skärmen för att ange koden och Webbadressen](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    Den **konton** skärmen i appen visar namnet på ditt konto och en sexsiffrig Verifieringskod. För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra någon från och med en kod flera gånger.

## <a name="add-your-google-account"></a>Lägg till ditt Google-konto

1. På din dator, Välj **kan inte SKANNA IT** från den **konfigurera Authenticator** sida med QR-koden.

    Den **kan inte skanna streckkod** visas med hemlig koden. Behåll den här sidan öppen så att du kan se den hemliga koden.

2. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och styra** ikonen i det övre högra hörnet, väljer **annat konto (Google, Facebook, etc.)** , och välj sedan **eller ange koden MANUELLT**.

3. Ange en **kontonamn** (till exempel Google) och Skriv den **hemlig nyckel** från steg 1 och välj sedan **Slutför**.

4. På den **konfigurera Authenticator** på datorn, ange den sexsiffriga Verifieringskod som angavs i appen för ditt Google-konto och välj sedan **Kontrollera**.

    Den **konton** skärmen i appen visar namnet på ditt konto och en sexsiffrig Verifieringskod. För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra någon från och med en kod flera gånger.

    >[!NOTE]
    >Mer information om verifiering av två faktorer och ditt Google-konto finns i [aktivera 2 Autentiseringssteget](https://support.google.com/accounts/answer/185839) och [mer information om 2 Autentiseringssteget](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Lägg till ditt Facebook-konto

1. På den **konfigureras via tredje part Authenticator** sida med QR-kod och en kod som skrivits för post i din app. Behåll den här sidan öppen så att du kan se koden.

2. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och styra** ikonen i det övre högra hörnet, väljer **annat konto (Google, Facebook, etc.)** , och välj sedan **eller ange koden MANUELLT**.

3. Ange en **kontonamn** (till exempel Facebook) och Skriv den **hemlig nyckel** från steg 1 och välj sedan **Slutför**.

4. På den **tvåfaktorsautentisering Authenticator** på datorn, ange den sexsiffriga Verifieringskod som angavs i appen för ditt Facebook-konto och välj sedan **Kontrollera**.

    Den **konton** skärmen i appen visar namnet på ditt konto och en sexsiffrig Verifieringskod. För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra någon från och med en kod flera gånger.

    >[!NOTE]
    >Mer information om verifiering av två faktorer och ditt Facebook-konto finns i [vad är tvåfaktorsautentisering och hur fungerar det?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-amazon-account"></a>Lägg till Amazon-konto
Du kan lägga till Amazon-konto genom att aktivera tvåfaktorsautentisering verifiering och sedan lägger du till kontot i appen.

1. På din dator, Välj **kan inte skanna streckkoden** från den **Välj hur du kommer att få koder** sida med QR-koden.

    Den **kan inte skanna streckkoden** meddelande visas med hemlig koden. Lämna det här meddelandet öppet så att du kan se den hemliga koden.

2. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och styra** ikonen i det övre högra hörnet, väljer **annat konto (Google, Facebook, etc.)** , och välj sedan **eller ange koden MANUELLT**.

3. Ange en **kontonamn** (till exempel Amazon) och Skriv den **hemlig nyckel** från steg 1 och välj sedan **Slutför**.

4. Slutför resten av registreringen, inklusive att lägga till en säkerhetskopiering verifieringsmetod, till exempel ett SMS och välj sedan **skicka kod**.

5. På den **lägga till en säkerhetskopiering verifieringsmetod** på datorn, skriver sexsiffrig verifieringskoden som tillhandahålls av din säkerhetskopiering verifieringsmetod för Amazon-konto och välj sedan **verifiera kod och fortsätta**.

6. På Almost sidan, bestämma om du vill gör datorn till en betrodd enhet och välj sedan **fattar. Aktivera tvåstegsverifiering**.

    Den **avancerade säkerhetsinställningar** visas, som visar information om din uppdaterade tvåfaktors-verifiering.

    >[!NOTE]
    >Mer information om verifiering av två faktorer och Amazon-konto finns i [om Tvåstegsverifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) och [logga in med Tvåstegsverifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).    

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton till appen, kan du logga in med Microsoft Authenticator-appen på din enhet. Mer information finns i [logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS, du kan även säkerhetskopiera dina autentiseringsuppgifter och appinställningar, till exempel ordningen på dina konton, som rör molnet. Mer information finns i [säkerhetskopiering och återställning med Microsoft Authenticator-appen](user-help-auth-app-backup-recovery.md).
