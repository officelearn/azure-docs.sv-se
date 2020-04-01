---
title: Konfigurera Twitter-autentisering
description: Lär dig hur du konfigurerar Twitter-autentisering som identitetsprovider för appen AppTjänst eller Azure Functions.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 3f85f30e0a64b6e39b905fc05503a445aa5876ba
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438002"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Konfigurera appen App Service eller Azure Functions för att använda Twitter-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service eller Azure Functions för att använda Twitter som autentiseringsleverantör.

För att slutföra proceduren i den här artikeln behöver du ett Twitter-konto som har en verifierad e-postadress och telefonnummer. Om du vill skapa ett nytt Twitter-konto går du till [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registrera din ansökan med Twitter

1. Logga in på [Azure-portalen] och gå till ditt program. Kopiera **webbadressen**. Du ska använda den för att konfigurera din Twitter-app.
1. Gå till [webbplatsen för Twitter-utvecklare,] logga in med dina Twitter-kontouppgifter och välj **Skapa en app**.
1. Ange **appens namn** och **programbeskrivningen** för den nya appen. Klistra in programmets **URL** i fältet **Webbadress.** I avsnittet **Motringningsadresser** anger du HTTPS-URL:en för `/.auth/login/twitter/callback`apptjänstappen och lägger till sökvägen . Till exempel `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Längst ned på sidan skriver du minst 100 tecken i **Berätta hur den här appen ska användas**och välj sedan **Skapa**. Klicka på **Skapa** igen i popup-programmet. Programinformationen visas.
1. Välj fliken **Nycklar och åtkomsttokens.**

   Anteckna dessa värden:
   - API-nyckel
   - HEMLIG API-nyckel

   > [!NOTE]
   > API-hemlighetsnyckeln är en viktig säkerhetsautentiseringsautentisering. Dela inte denna hemlighet med någon eller distribuera den med din app.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Lägg till Twitter-information i din ansökan

1. Gå till ditt program i [Azure-portalen].
1. Välj **Inställningar** > **Autentisering /Auktorisering**och kontrollera att **apptjänstautentiseringen** är **på**.
1. Välj **Twitter**.
1. Klistra in `API key` `API secret key` de värden och som du fick tidigare.
1. Välj **OK**.

   ![Skärmbild av Twitter-inställningar för mobilapp][1]

   Som standard tillhandahåller App Service autentisering men begränsar inte denuktoriserat åtkomsten till webbplatsens innehåll och API:er. Du måste auktorisera användare i din appkod.

1. (Valfritt) Om du vill begränsa åtkomsten till din webbplats till endast användare som autentiserats av Twitter anger du **åtgärder som ska vidtas när begäran inte autentiseras** på **Twitter**. När du ställer in den här funktionen kräver appen att alla begäranden ska autentiseras. Det omdirigerar också alla oautentiserade förfrågningar till Twitter för autentisering.

   > [!CAUTION]
   > Att begränsa åtkomsten på det här sättet gäller alla samtal till din app, vilket kanske inte är önskvärt för appar som har en allmänt tillgänglig startsida, som i många ensidiga program. För sådana program kan **tillåt anonyma begäranden (ingen åtgärd)** att föredra så att appen startar själva autentiseringen manuellt. Mer information finns i [Autentiseringsflöde](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Twitter för autentisering i din app.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
