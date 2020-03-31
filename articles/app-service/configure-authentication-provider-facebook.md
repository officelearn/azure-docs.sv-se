---
title: Konfigurera Facebook-autentisering
description: Läs om hur du konfigurerar Facebook-autentisering som identitetsleverantör för apptjänstappen.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: cd9c8a1bab3616b9b4eb1fe97ee3a9b2307ba77b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671938"
---
# <a name="configure-your-app-service-app-to-use-facebook-login"></a>Konfigurera appen App Service så att den använder Facebook-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service för att använda Facebook som autentiseringsleverantör.

För att slutföra proceduren i den här artikeln behöver du ett Facebook-konto som har en verifierad e-postadress och ett mobiltelefonnummer. Om du vill skapa ett nytt Facebook-konto går du till [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrera din ansökan hos Facebook

1. Gå till [Webbplatsen för Facebook-utvecklare] och logga in med dina Facebook-kontouppgifter.

   Om du inte har ett Facebook för utvecklare-konto väljer du **Kom igång** och följer registreringsstegen.
1. Välj **Lägg** > till**min app Lägg till ny app**.
1. I fältet **Visningsnamn:**
   1. Skriv ett unikt namn på appen.
   1. Ange din **kontakt e-post**.
   1. Välj **Skapa app-ID**.
   1. Slutför säkerhetskontrollen.

   Utvecklarinstrumentpanelen för din nya Facebook-app öppnas.
1. Välj **Instrumentpanel** > **Facebook-inloggning** > **Konfigurera** > **webben**.
1. I den vänstra navigeringen under **Facebook-inloggning**väljer du **Inställningar**.
1. Ange i fältet **Giltig OAuth-omdirigering av URI:er** `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. Kom ihåg `<app-name>` att ersätta med namnet på din Azure App Service-app.
1. Välj **Spara ändringar**.
1. Välj **Inställningar** > **Basic**i den vänstra rutan . 
1. Välj **Visa**i fältet **Apphemlighet** . Kopiera värdena för **App-ID** och **App Secret**. Du använder dem senare för att konfigurera appen App Service i Azure.

   > [!IMPORTANT]
   > Apphemligheten är en viktig säkerhetsautentiseringsinformation. Dela inte denna hemlighet med någon eller distribuera den inom ett klientprogram.
   >

1. Det Facebook-konto som du använde för att registrera programmet är administratör för appen. Nu kan bara administratörer logga in på det här programmet.

   Om du vill autentisera andra Facebook-konton väljer du **Appgranskning** och aktiverar **Gör \<ditt-app-namn> offentligt** för att allmänheten ska kunna komma åt appen med hjälp av Facebook-autentisering.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Lägga till Facebook-information i din ansökan

1. Logga in på [Azure-portalen] och navigera till appen App Service.
1. Välj **Inställningar** > **Autentisering /Auktorisering**och kontrollera att **apptjänstautentiseringen** är **på**.
1. Välj **Facebook**och klistra sedan in de app-ID- och Apphemliga värden som du tidigare fått. Aktivera alla scope som behövs av ditt program.
1. Välj **OK**.

   ![Skärmbild av Facebook-inställningar för mobilapp][0]

    Som standard tillhandahåller App Service autentisering, men det begränsar inte den auktoriserat åtkomsten till webbplatsens innehåll och API:er. Du måste auktorisera användare i din appkod.
1. (Valfritt) Om du bara vill begränsa åtkomsten till användare som autentiserats av Facebook anger du **åtgärd som ska vidtas när begäran inte autentiseras** till **Facebook**. När du ställer in den här funktionen kräver appen att alla begäranden ska autentiseras. Det omdirigerar också alla oautentiserade förfrågningar till Facebook för autentisering.

   > [!CAUTION]
   > Att begränsa åtkomsten på det här sättet gäller alla samtal till din app, vilket kanske inte är önskvärt för appar som har en allmänt tillgänglig startsida, som i många ensidiga program. För sådana program kan **tillåt anonyma begäranden (ingen åtgärd)** att föredra så att appen startar själva autentiseringen manuellt. Mer information finns i [Autentiseringsflöde](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Nu är du redo att använda Facebook för autentisering i din app.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portal]: https://portal.azure.com/
