---
title: Konfigurera Facebook - autentisering i Azure App Service
description: Lär dig mer om att konfigurera Facebook-autentisering för App Services-appen.
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
ms.openlocfilehash: e91d55c29d325301b8ac70ddc63fb408961fbb2c
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742967"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Så här konfigurerar du App Service-programmet för att använda Facebook-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Facebook som en autentiseringsprovider.

För att slutföra den här proceduren i det här avsnittet måste du ha en Facebook-konto som har en verifierad e-postadress och ett mobiltelefonnummer. Om du vill skapa ett nytt Facebook-konto går du till [facebook.com].

## <a name="register"> </a>Registrera ditt program med Facebook
1. Navigera till den [Facebook-utvecklare] webbplatsen och logga in med ditt Facebook kontoautentiseringsuppgifter.
3. (Valfritt) Om du inte har ett Facebook för utvecklare konto, klickar du på **börjar** och följer stegen för registrering.
4. Klicka på **Mina appar** > **Lägg till ny App**.
5. I **visningsnamn**, Skriv ett unikt namn för din app. Tillhandahåller även din **kontakta e-post**, och klicka sedan på **skapa App-ID** och slutför säkerhetskontrollen. Detta tar dig till instrumentpanelen för utvecklare för din nya Facebook-app.
6. Klicka på **instrumentpanelen** > **Facebook-inloggning** > **konfigurera** > **Web**.
1. I det vänstra navigeringsfältet under **Facebook-inloggning**, klickar du på **inställningar**.
1. I **giltig OAuth omdirigerings-URI: er**, typ `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` och Ersätt  *\<appens namn->* med namnet på din Azure App Service-app. Klicka på **Spara ändringar**.
8. I det vänstra navigeringsfältet, klickar du på **inställningar** > **grundläggande**. På den **Apphemlighet** klickar **visa**. Kopiera värdena för **App-ID** och **Apphemlighet**. Du använder dessa senare för att konfigurera din App Service-app i Azure.
   
   > [!IMPORTANT]
   > Apphemligheten är en viktig säkerhetsuppgift för autentisering. Inte dela den här hemligheten med vem som helst och distribuera det i ett klientprogram.
   > 
   > 
9. Facebook-konto som användes för att registrera programmet är en administratör av appen. Endast administratörer kan nu logga in på det här programmet. För att autentisera andra Facebook-konton, klickar du på **App granska** och aktivera **gör \<your app name > offentliga** att aktivera den allmänna offentlig åtkomst med hjälp av Facebook-autentisering.

## <a name="secrets"> </a>Lägga till Facebook-information i ditt program
1. Logga in på den [Azure Portal] och gå till App Service-appen. Klicka på **inställningar** > **autentisering / auktorisering**, och se till att **Apptjänstautentisering** är **på**.
2. Klicka på **Facebook**, klistra in App-ID och en Apphemlighet värdena som du hämtade tidigare, om du vill aktivera alla omfattningar som behövs för din App och sedan klickar du på **OK**.
   
    ![][0]
   
    Som standard, App Service ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din kod.
3. (Valfritt) Om du vill begränsa åtkomsten till din plats för att endast användare som autentiseras av Facebook, ange **åtgärd att vidta när begäran inte har autentiserats** till **Facebook**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Facebook för autentisering.
4. När konfigurera autentisering klickar du på **spara**.

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
