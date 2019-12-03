---
title: Konfigurera Facebook-autentisering
description: Lär dig hur du konfigurerar Facebook-autentisering som en identitets leverantör för din App Service-app.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: cd9c8a1bab3616b9b4eb1fe97ee3a9b2307ba77b
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671938"
---
# <a name="configure-your-app-service-app-to-use-facebook-login"></a>Konfigurera din App Service-app att använda Facebook-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service att använda Facebook som autentiseringsprovider.

För att kunna slutföra proceduren i den här artikeln behöver du ett Facebook-konto som har en verifierad e-postadress och ett mobiltelefon nummer. Om du vill skapa ett nytt Facebook-konto går du till [Facebook.com].

## <a name="register"> </a>Registrera ditt program med Facebook

1. Gå till [Facebook-utvecklare] webbplats och logga in med dina Facebook-kontoautentiseringsuppgifter.

   Om du inte har ett Facebook för utvecklare-konto väljer du **Kom igång** och följer registrerings stegen.
1. Välj **Mina appar** > **Lägg till ny app**.
1. I fältet **visnings namn** :
   1. Ange ett unikt namn för din app.
   1. Ange din **kontakt-e-postadress**.
   1. Välj **skapa app-ID**.
   1. Slutför säkerhets kontrollen.

   Developer-instrumentpanelen för din nya Facebook-app öppnas.
1. Välj **instrument panel** > **Facebook-inloggning** > **Konfigurera** > **webb**.
1. I det vänstra navigerings fältet under **Facebook-inloggning**väljer du **Inställningar**.
1. I fältet **giltiga OAuth-omdirigerings-URI** anger du `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. Kom ihåg att ersätta `<app-name>` med namnet på din Azure App Service-app.
1. Välj **Spara ändringar**.
1. I det vänstra fönstret väljer du **inställningar** > **Basic**. 
1. I fältet **app Secret** väljer du **Visa**. Kopiera värdena för **app-ID** och **app Secret**. Du kan använda dem senare för att konfigurera din App Service-app i Azure.

   > [!IMPORTANT]
   > Appens hemlighet är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.
   >

1. Det Facebook-konto som du använde för att registrera programmet är en administratör för appen. I det här läget kan endast administratörer logga in till det här programmet.

   Om du vill autentisera andra Facebook-konton väljer du **program granskning** och aktiverar \<appens **namn > offentlig** för att ge allmänheten till gång till appen med hjälp av Facebook-autentisering.

## <a name="secrets"> </a>Lägga till Facebook-information i ditt program

1. Logga in på [Azure-portalen] och navigera till din app service-app.
1. Välj **inställningar** > **autentisering/auktorisering**och se till att **App Service autentisering** är **aktiverat**.
1. Välj **Facebook**och klistra in i app-ID och appens hemliga värden som du har fått tidigare. Aktivera alla omfattningar som krävs av ditt program.
1. Välj **OK**.

   ![Skärm bild av Facebook-inställningar för mobilapp][0]

    Som standard tillhandahåller App Service autentisering, men den begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din app-kod.
1. Valfritt Om du bara vill begränsa åtkomsten till användare som autentiserats av Facebook anger du **åtgärd som ska vidtas när begäran inte autentiseras** till **Facebook**. När du ställer in den här funktionen kräver appen att alla begär Anden ska autentiseras. Det omdirigerar också alla oautentiserade begär anden till Facebook för autentisering.

   > [!CAUTION]
   > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som har en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program **tillåts anonyma begär Anden (ingen åtgärd)** , så att appen manuellt startar själva autentiseringen. Mer information finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Facebook för autentisering i din app.

## <a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portalen]: https://portal.azure.com/
