---
title: Konfigurera Googles autentisering
description: Lär dig hur du konfigurerar Googles autentisering som identitetsleverantör för appen AppTjänst eller Azure Functions.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: bb6b8eebef3247cf2c39ed4b111296e1e0521a74
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437981"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Konfigurera appen App Service eller Azure Functions så att den använder Google-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service eller Azure Functions för att använda Google som autentiseringsleverantör.

Om du vill slutföra proceduren i det här avsnittet måste du ha ett Google-konto som har en verifierad e-postadress. Gå till [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) om du vill skapa ett nytt Google-konto.

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registrera din ansökan hos Google

1. Följ Googles dokumentation på [Googles inloggningsappar för att](https://developers.google.com/identity/sign-in/web/server-side-flow) skapa ett klient-ID och klienthemlighet. Du behöver inte göra några kodändringar. Använd bara följande information:
    - För **Auktoriserade JavaScript-ursprung**använder du `https://<app-name>.azurewebsites.net` med namnet på din app i * \<>. *
    - För **auktoriserad redirect-URI**använder du `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Kopiera app-ID:t och appens hemliga värden.

    > [!IMPORTANT]
    > Apphemligheten är en viktig säkerhetsautentiseringsautentisering. Dela inte denna hemlighet med någon eller distribuera den inom ett klientprogram.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Lägga till Google-information i ditt program

1. Gå till appen App Service i [Azure-portalen.]
1. Välj **Inställningar** > **Autentisering /Auktorisering**och kontrollera att **apptjänstautentiseringen** är **på**.
1. Välj **Google**och klistra sedan in de värden för app-ID och App Secret som du fick tidigare. Aktivera alla scope som behövs av ditt program.
1. Välj **OK**.

   App Service tillhandahåller autentisering men begränsar inte den behöriga åtkomsten till webbplatsens innehåll och API:er. Mer information finns i [Auktorisera eller neka användare](app-service-authentication-how-to.md#authorize-or-deny-users).

1. (Valfritt) Om du bara vill begränsa webbplatsåtkomsten till användare som autentiserats av Google anger du **att Åtgärd ska vidtas när begäran inte autentiseras** till **Google**. När du ställer in den här funktionen kräver appen att alla begäranden autentiseras. Det omdirigerar också alla oautentiserade förfrågningar till Google för autentisering.

    > [!CAUTION]
    > Att begränsa åtkomsten på det här sättet gäller alla samtal till din app, vilket kanske inte är önskvärt för appar som har en allmänt tillgänglig startsida, som i många ensidiga program. För sådana program kan **tillåt anonyma begäranden (ingen åtgärd)** att föredra så att appen startar själva autentiseringen manuellt. Mer information finns i [Autentiseringsflöde](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Google för autentisering i din app.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

