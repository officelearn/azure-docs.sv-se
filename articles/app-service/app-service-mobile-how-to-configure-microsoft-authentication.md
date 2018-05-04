---
title: Hur du konfigurerar Account autentisering för tillämpningsprogrammet Apptjänster
description: Lär dig hur du konfigurerar Account autentisering för ditt program med App-tjänster.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 4fb5bdf30502dbca3eba961165a1ab643427abd6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Så här konfigurerar du din App tjänstprogram att använda Account inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Account som en autentiseringsprovider. 

## <a name="register-microsoft-account"> </a>Registrera din app med Microsoft-konto
1. Logga in på den [Azure-portalen], och navigera till programmet. Kopiera ditt **URL**, som senare användas för att konfigurera din app med Account.
2. Navigera till den [Mina program] i Microsoft Account Developer Center och logga in med ditt Microsoft-konto om det behövs.
3. Klicka på **Lägg till en app**, ange ett programnamn och på **skapa**.
4. Anteckna den **program-ID**, som du behöver senare. 
5. Klicka på under ”plattformar”, **lägga till plattformen** och välj ”Web”.
6. Ange slutpunkten för ditt program under ”omdirigerings-URI: er” och klicka sedan på **spara**. 
   
   > [!NOTE]
   > Din omdirigering URI är URL-Adressen till ditt program läggas till med sökvägen */.auth/login/microsoftaccount/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Kontrollera att du använder HTTPS-schema.
   
7. Klicka på under ”programmet hemligheter”, **generera nya lösenord**. Anteckna värdet som visas. När du lämnar sidan, kommer den inte att visas igen.

    > [!IMPORTANT]
    > Lösenordet är en viktig säkerhetsuppgift för autentisering. Inte dela lösenordet med någon eller distribuera den i ett klientprogram.

## <a name="secrets"> </a>Lägga till information om Account tillämpningsprogrammet Apptjänst
1. I den [Azure-portalen]navigerar du till ditt program, klickar du på **inställningar** > **autentisering / auktorisering**.
2. Om autentisering / auktorisering är inte aktiverad, växlar den **på**.
3. Klicka på **Microsoft-konto**. Klistra in i program-ID och lösenord värden som du hämtade tidigare och välja att aktivera alla scope som krävs för ditt program. Klicka sedan på **OK**.
   
    ![][1]
   
    Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod.
4. (Valfritt) Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiseras av Microsoft-konto, ange **åtgärd att vidta när begäran inte har autentiserats** till **Account**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Microsoft-konto för autentisering.
5. Klicka på **Spara**.

Du är nu redo att använda Account för autentisering i appen.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mina program]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portalen]: https://portal.azure.com/
