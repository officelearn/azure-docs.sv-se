---
title: Konfigurera Twitter-autentisering – Azure App Service
description: Lär dig hur du konfigurerar Twitter-autentisering för ditt App Services-program.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 590be42d5ba96133739d511b5a0a6b3e5a5634ff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033618"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Så här konfigurerar du ditt App Service program så att Twitter-inloggning används
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service att använda Twitter som autentiseringsprovider.

För att slutföra proceduren i det här avsnittet måste du ha ett Twitter-konto som har en verifierad e-postadress och ett telefonnummer. Om du vill skapa ett nytt Twitter-konto går du till <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">Twitter.com</a>.

## <a name="register"> </a>Registrera ditt program med Twitter
1. Logga in på [Azure Portal]och navigera till ditt program. Kopiera din **URL**. Du kommer att använda den här för att konfigurera din Twitter-app.
2. Gå till webbplatsen för [Twitter-utvecklare] och logga in med dina Twitter-konto uppgifter och klicka på **Skapa ny app**.
3. Ange **namnet** och en **Beskrivning** för den nya appen. Klistra in programmets **URL** för **webbplats** svärdet. För återanrops- **URL**: en klistrar du sedan in den **URL** som du kopierade tidigare. Det här är din mobilapp-gateway som lagts till med sökvägen */.auth/login/Twitter/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Kontrol lera att du använder HTTPS-schemat.
4. Läs och godkänn villkoren längst ned på sidan. Klicka sedan på **skapa ditt Twitter-program**. Detta registrerar appen visar programinformationen.
5. Klicka på fliken **Inställningar** , markera **Tillåt att det här programmet används för att logga in med Twitter**och klicka sedan på **Uppdatera inställningar**.
6. Välj fliken **nycklar och åtkomst** -token. Anteckna värdena för **konsument nyckel (API-nyckel)** och **konsument hemlighet (API-hemlighet)** .
   
   > [!NOTE]
   > Konsument hemligheten är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den med din app.
   > 
   > 

## <a name="secrets"> </a>Lägg till Twitter-information i ditt program
1. Gå tillbaka till ditt program i [Azure Portal]. Klicka på **Inställningar**och sedan **autentisering/auktorisering**.
2. Om funktionen autentisering/auktorisering inte är aktive rad aktiverar du växeln till **på**.
3. Klicka på **Twitter**. Klistra in i app-ID och appens hemliga värden som du har hämtat tidigare. Klicka sedan på **OK**.
   
   ![][1]
   
   Som standard tillhandahåller App Service autentisering men begränsar inte tillåten åtkomst till ditt webbplats innehåll och API: er. Du måste auktorisera användare i din app-kod.
4. Valfritt Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiserats av Twitter, anger **du åtgärd som ska vidtas när förfrågan inte autentiseras** till **Twitter**. Detta kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Twitter för autentisering.

> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program kan du **tillåta anonyma begär Anden (ingen åtgärd)** , med appen manuellt startar inloggningen, enligt beskrivningen [här](overview-authentication-authorization.md#authentication-flow).

5. Klicka på **Spara**.

Du är nu redo att använda Twitter för autentisering i din app.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
