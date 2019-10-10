---
title: Konfigurera Twitter-autentisering – Azure App Service
description: Lär dig hur du konfigurerar Twitter-autentisering för din App Service-app.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 02008b7dc1609a5f28ac6ba2a582933a96428198
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176961"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Konfigurera din App Service-app att använda Twitter-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service att använda Twitter som autentiseringsprovider.

För att kunna slutföra proceduren i den här artikeln behöver du ett Twitter-konto som har en verifierad e-postadress och ett telefonnummer. Om du vill skapa ett nytt Twitter-konto går du till [Twitter.com].

## <a name="register"> </a>Registrera ditt program med Twitter

1. Logga in på [Azure-portalen] och gå till ditt program. Kopiera din **URL**. Du använder den för att konfigurera din Twitter-app.
1. Gå till webbplatsen för [Twitter-utvecklare] och logga in med dina Twitter-konto uppgifter och välj **Skapa ny app**.
1. Ange ett **namn** och en **Beskrivning** för den nya appen. Klistra in programmets **URL** i fältet **webbplats** . I fältet **återanrops-URL** anger du URL: en för din app service-app och lägger till sökvägen `/.auth/login/aad/callback`. Till exempel `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Se till att använda HTTPS-schemat.
1. Läs och godkänn villkoren längst ned på sidan. Välj **skapa ditt Twitter-program**. Programinformationen visas.
1. Välj fliken **Inställningar** , markera **Tillåt att det här programmet används för att logga in med Twitter**och välj sedan **Uppdatera inställningar**.
1. Välj fliken **nycklar och åtkomst-token** .

   Anteckna dessa värden:
   - Konsument nyckel (API-nyckel)
   - Konsument hemlighet (API-hemlighet)

   > [!NOTE]
   > Konsument hemligheten är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den med din app.

## <a name="secrets"> </a>Lägg till Twitter-information i ditt program

1. Gå till ditt program i [Azure-portalen].
1. Välj **inställningar** > **autentisering/auktorisering**och se till att **App Service autentisering** är **aktiverat**.
1. Välj **Twitter**.
1. Klistra in värdena `API Key` och `API Secret` som du har fått tidigare.
1. Välj **OK**.

   ![Skärm bild av Twitter-inställningar för mobilapp][1]

   Som standard tillhandahåller App Service autentisering, men begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din app-kod.

1. Valfritt Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiserats av Twitter, anger **du åtgärd som ska vidtas när förfrågan inte autentiseras** till **Twitter**. När du ställer in den här funktionen kräver appen att alla begär Anden ska autentiseras. Det omdirigerar också alla oautentiserade begär anden till Twitter för autentisering.

   > [!CAUTION]
   > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som har en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program **tillåts anonyma begär Anden (ingen åtgärd)** , så att appen manuellt startar själva autentiseringen. Mer information finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Twitter för autentisering i din app.

## <a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure-portalen]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
