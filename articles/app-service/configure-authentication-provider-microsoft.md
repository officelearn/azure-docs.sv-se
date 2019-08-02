---
title: Konfigurera autentisering av Microsoft-konto – Azure App Service
description: Lär dig hur du konfigurerar autentisering med Microsoft-konto för ditt App Services-program.
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
ms.custom: seodec18
ms.openlocfilehash: 10b661f0c4b7dc45284b907e83df3c0372f97cab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561538"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Så här konfigurerar du ditt App Service program för att använda inloggning med Microsoft-konto
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service att använda Microsoft-konto som autentiseringsprovider. 

## <a name="register-microsoft-account"> </a>Registrera din app med Microsoft-konto
1. Logga in på [Azure Portal]och navigera till ditt program. Kopiera din **URL**, som senare används för att konfigurera din app med Microsoft-konto.
2. Gå till [**Appregistreringar**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)och logga in med ditt Microsoft-konto, om det behövs.
3. Klicka på **Lägg till en app**och skriv sedan ett program namn och klicka på **skapa**.
4. Anteckna **program-ID: t**eftersom du kommer att behöva det senare. 
5. Under "plattformar" klickar du på **Lägg till plattform** och väljer "webb".
6. Under "omdirigerings-URI" anger du slut punkten för ditt program och klickar sedan på **Spara**. 
   
   > [!NOTE]
   > Din omdirigerings-URI är URL: en för ditt program som lagts till med sökvägen */.auth/login/MicrosoftAccount/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Kontrol lera att du använder HTTPS-schemat.
   
7. Under "program hemligheter" klickar du på **Skapa nytt lösen ord**. Anteckna värdet som visas. När du lämnar sidan visas den inte igen.

    > [!IMPORTANT]
    > Lösen ordet är en viktig säkerhets autentiseringsuppgift. Dela inte lösen ordet med någon eller distribuera det i ett klient program.
    
8. Klicka på **Spara**

## <a name="secrets"> </a>Lägg till information om Microsoft-konto till ditt App Service program
1. Gå tillbaka till ditt program i [Azure Portal], klicka på **Inställningar** > **autentisering/auktorisering**.
2. Om funktionen autentisering/auktorisering inte är aktive rad, så aktivera **den.**
3. Klicka på **Microsoft-konto**. Klistra in värdena för program-ID och lösen ord som du har fått tidigare, och eventuellt aktivera alla omfattningar som programmet kräver. Klicka sedan på **OK**.
   
    ![][1]
   
    Som standard tillhandahåller App Service autentisering men begränsar inte tillåten åtkomst till ditt webbplats innehåll och API: er. Du måste auktorisera användare i din app-kod.
4. Valfritt Om du vill begränsa åtkomsten till din webbplats till endast användare som autentiserats av Microsoft-konto anger **du åtgärd som ska vidtas när förfrågan inte autentiseras** till **Microsoft-kontot**. Detta kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Microsoft-konto för autentisering.
5. Klicka på **Spara**.

Du är nu redo att använda Microsoft-kontot för autentisering i din app.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
