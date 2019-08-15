---
title: Konfigurera Facebook-autentisering – Azure App Service
description: Lär dig hur du konfigurerar Facebook-autentisering för ditt App Services-program.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: c9767ff1e6f0b31270f37842cf99d71cab561505
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033842"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Så här konfigurerar du App Service-programmet för att använda Facebook-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service att använda Facebook som autentiseringsprovider.

För att slutföra proceduren i det här avsnittet måste du ha ett Facebook-konto som har en verifierad e-postadress och ett mobiltelefon nummer. Om du vill skapa ett nytt Facebook-konto går du till [Facebook.com].

## <a name="register"> </a>Registrera ditt program med Facebook
1. Navigera till [Facebook-utvecklare] -utvecklarens webbplats och logga in med dina Facebook-kontoautentiseringsuppgifter.
3. Valfritt Om du inte har ett Facebook för utvecklare-konto klickar du på **Kom igång** och följer registrerings stegen.
4. Klicka på **Mina appar** > **Lägg till ny app**.
5. I **visnings namn**, ange ett unikt namn för din app. Ange också **e-** postadressen och klicka sedan på **skapa app-ID** och slutför säkerhets kontrollen. Det tar dig till Developer-instrumentpanelen för din nya Facebook-app.
6. Klicka på **instrument panel** > **Facebook-inloggning** > **Konfigurera** > **webb**.
1. Klicka på **Inställningar**i den vänstra navigeringen under **Facebook-inloggning**.
1. I **giltiga OAuth**-omdirigerings `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` -URI skriver och ersätter  *\<du App-Name->* med namnet på din Azure App Service-app. Klicka på **Spara ändringar**.
8. Klicka på **Inställningar** > **Basic**i det vänstra navigerings fältet. I fältet **app Secret** klickar du på **Visa**. Kopiera värdena för **app-ID** och **app Secret**. Du använder dessa senare för att konfigurera din App Service-app i Azure.
   
   > [!IMPORTANT]
   > Appens hemlighet är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.
   > 
   > 
9. Det Facebook-konto som användes för att registrera programmet är en administratör av appen. I det här läget kan endast administratörer logga in till det här programmet. Om du vill autentisera andra Facebook-konton klickar du på **program granskning** och  **\<gör ditt-App-Name > Public** för att aktivera allmän offentlig åtkomst med Facebook-autentisering.

## <a name="secrets"> </a>Lägga till Facebook-information i ditt program
1. Logga in på [Azure Portal] och navigera till din app service-app. Klicka på **Inställningar** > **autentisering/auktorisering**och kontrol lera att **App Service autentisering** är **aktiverat**.
2. Klicka på **Facebook**, klistra in app-ID: t och de hemliga värdena för appen som du har fått tidigare, om du vill aktivera eventuella omfattningar som behövs för ditt program och klicka sedan på **OK**.
   
    ![][0]
   
    Som standard tillhandahåller App Service autentisering men begränsar inte tillåten åtkomst till ditt webbplats innehåll och API: er. Du måste auktorisera användare i din app-kod.
3. Valfritt Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiserats av Facebook, anger **du åtgärd som ska vidtas när förfrågan inte autentiseras** till **Facebook**. Detta kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Facebook för autentisering.
 
> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program kan du **tillåta anonyma begär Anden (ingen åtgärd)** , med appen manuellt startar inloggningen, enligt beskrivningen [här](overview-authentication-authorization.md#authentication-flow).

4. När du är färdig med konfigurationen av autentisering klickar du på **Spara**.

Du är nu redo att använda Facebook för autentisering i din app.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
