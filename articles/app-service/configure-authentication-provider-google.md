---
title: Konfigurera Google-autentisering
description: Lär dig hur du konfigurerar Google-autentisering som en identitets leverantör för din App Service-app.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670815"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Konfigurera din App Service-app att använda Google-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

I det här avsnittet visas hur du konfigurerar Azure App Service att använda Google som en autentiseringsprovider.

För att slutföra proceduren i det här avsnittet måste du ha ett Google-konto som har en verifierad e-postadress. Gå till [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) om du vill skapa ett nytt Google-konto.

## <a name="register"> </a>Registrera ditt program med Google

1. Följ Google-dokumentationen på [Google-inloggningen för appar på Server sidan](https://developers.google.com/identity/sign-in/web/server-side-flow) för att skapa ett klient-ID och klient hemlighet. Du behöver inte göra några kod ändringar. Använd bara följande information:
    - För **behöriga JavaScript-ursprung**använder du `https://<app-name>.azurewebsites.net` med namnet på din app i *\<app-Name >* .
    - Använd `https://<app-name>.azurewebsites.net/.auth/login/google/callback`för **auktoriserad omdirigerings-URI**.
1. Kopiera app-ID och appens hemliga värden.

    > [!IMPORTANT]
    > Appens hemlighet är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.

## <a name="secrets"> </a>Lägga till Google information till ditt program

1. Gå till din App Service-app i [Azure-portalen].
1. Välj **inställningar** > **autentisering/auktorisering**och se till att **App Service autentisering** är **aktiverat**.
1. Välj **Google**och klistra in i app-ID och appens hemliga värden som du har fått tidigare. Aktivera alla omfattningar som krävs av ditt program.
1. Välj **OK**.

   App Service tillhandahåller autentisering men begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Mer information finns i [auktorisera eller neka användare](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Valfritt För att begränsa plats åtkomsten enbart till användare som autentiserats av Google, ange **åtgärd som ska vidtas när begäran inte autentiseras** till **Google**. När du ställer in den här funktionen kräver appen att alla begär Anden autentiseras. Det omdirigerar också alla oautentiserade begär anden till Google för autentisering.

    > [!CAUTION]
    > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som har en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program **tillåts anonyma begär Anden (ingen åtgärd)** , så att appen manuellt startar själva autentiseringen. Mer information finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Google för autentisering i din app.

## <a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portalen]: https://portal.azure.com/

