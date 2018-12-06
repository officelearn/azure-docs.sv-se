---
title: Så här konfigurerar du Account autentisering för App Services-appen
description: Lär dig hur du konfigurerar Account autentisering för App Services-appen.
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
ms.openlocfilehash: be204c8c20825aab75e19acd0157b427ef4a388c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968670"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Så här konfigurerar du App Service-programmet att använda Microsoft Account login
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Account som en autentiseringsprovider. 

## <a name="register-microsoft-account"> </a>Registrera din app med Microsoft-konto
1. Logga in på den [Azure Portal], och navigera till ditt program. Kopiera din **URL**, som senare användas för att konfigurera din app med Account.
2. Navigera till den [Mina program] i Microsoft Account Developer Center och logga in med ditt Microsoft-konto om det behövs.
3. Klicka på **lägga till en app**skriver du namnet på ett program och klicka på **skapa**.
4. Anteckna den **program-ID**, som du behöver senare. 
5. Klicka på under ”plattformar”, **Lägg till plattform** och välj ”Web”.
6. Ange slutpunkten för ditt program under ”omdirigerings-URI” och sedan på **spara**. 
   
   > [!NOTE]
   > Din omdirigerings-URI: N är URL: en för ditt program läggas till med sökvägen */.auth/login/microsoftaccount/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Kontrollera att du använder HTTPS-schema.
   
7. Klicka på under ”Programhemligheter” **generera nytt lösenord**. Anteckna värdet som visas. När du lämnar sidan, kommer det inte att visas igen.

    > [!IMPORTANT]
    > Lösenordet är en viktig säkerhetsuppgift för autentisering. Inte dela lösenordet med vem som helst och distribuera det i ett klientprogram.
    
8. Klicka på **Spara**

## <a name="secrets"> </a>Lägga till information om Account App Service-programmet
1. I den [Azure Portal]navigerar du till ditt program, klicka på **inställningar** > **autentisering / auktorisering**.
2. Om autentisering / auktorisering är inte aktiverad, stänga av **på**.
3. Klicka på **Microsoftkonto**. Klistra in i program-ID och lösenord värden som du hämtade tidigare och välja att eventuella scope som krävs för ditt program. Klicka sedan på **OK**.
   
    ![][1]
   
    Som standard, App Service ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din kod.
4. (Valfritt) Om du vill begränsa åtkomsten till din plats för att endast användare som autentiseras av Microsoft-konto, ange **åtgärd att vidta när begäran inte har autentiserats** till **Account**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Microsoft-konto för autentisering.
5. Klicka på **Spara**.

Du är nu redo att använda Account för autentisering i din app.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mina program]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
