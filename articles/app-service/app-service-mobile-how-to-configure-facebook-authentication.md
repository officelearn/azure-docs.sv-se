---
title: "Hur du konfigurerar Facebook-autentisering för tillämpningsprogrammet Apptjänster"
description: "Lär dig hur du konfigurerar Facebook-autentisering för tillämpningsprogrammet Apptjänster."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: c1b4c91d384c56c4f55bf8d31ced250f51c0d837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Så här konfigurerar du App Service-programmet för att använda Facebook-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Facebook som en autentiseringsprovider.

Du måste ha en Facebook-konto som har en verifierad e-postadress och ett mobiltelefonnummer för att slutföra proceduren i det här avsnittet. Om du vill skapa ett nytt Facebook-konto går du till [facebook.com].

## <a name="register"></a>Registrera programmet med Facebook
1. Logga in på den [Azure-portalen], och navigera till programmet. Kopiera ditt **URL**. Du använder detta för att konfigurera Facebook-app.
2. I ett nytt webbläsarfönster navigerar du till den [Facebook utvecklare] webbplatsen och logga in med dina Facebook kontoautentiseringsuppgifter.
3. (Valfritt) Om du inte redan har registrerat, klickar du på **appar** > **registrera som en utvecklare**, acceptera principen och gör registreringen.
4. Klicka på **Mina appar** > **lägga till en ny App** > **webbplats** > **hoppa över och skapa App-ID**. 
5. I **visningsnamn**, ange ett unikt namn för din app typen din **kontakta e-post**, Välj en **kategori** för din app, klicka sedan på **skapa App-ID**och slutföra säkerhetskontrollen. Då kommer du till instrumentpanelen för utvecklare för din nya Facebook-app.
6. Klicka på under ”Facebook inloggning”, **Kom igång**. Lägg till programmets **omdirigerings-URI** till **omdirigerings-URI: er för giltig OAuth**, klicka på **spara ändringar**. 
   
   > [!NOTE]
   > Din omdirigering URI är URL-Adressen till ditt program läggas till med sökvägen */.auth/login/facebook/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Kontrollera att du använder HTTPS-schema.
   > 
   > 
7. I det vänstra navigeringsfönstret klickar du på **inställningar**. På den **App hemlighet** klickar **visa**, ange ditt lösenord om det begärs och anteckna värdena för **App-ID** och **App hemlighet** . Du använder dessa senare för att konfigurera ditt program i Azure.
   
   > [!IMPORTANT]
   > Hemligheten som app är en viktig säkerhetsuppgift för autentisering. Dela den här hemligheten med någon eller inte distribuera inom ett klientprogram.
   > 
   > 
8. Facebook-konto som användes för att registrera programmet är administratör för appen. Endast administratörer kan nu logga in på det här programmet. För att autentisera andra Facebook-konton klickar du på **App granska** och aktivera **Se < appens-namn > offentliga** att aktivera allmän allmän åtkomst med hjälp av Facebook-autentisering.

## <a name="secrets"></a>Lägg till Facebook-information för ditt program
1. I den [Azure-portalen], navigera till programmet. Klicka på **inställningar** > **autentisering / auktorisering**, och se till att **App autentiseringen av tjänsten** är **på**.
2. Klicka på **Facebook**, klistra in i appen hemligheten och App-ID-värden som du fick tidigare, om du vill aktivera alla scope som behövs i programmet och sedan klickar du på **OK**.
   
    ![][0]
   
    Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod.
3. (Valfritt) Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiseras av Facebook, ange **åtgärd att vidta när begäran inte har autentiserats** till **Facebook**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Facebook för autentisering.
4. När konfigurera autentisering klickar du på **spara**.

Du är nu redo att använda Facebook för autentisering i appen.

## <a name="related-content"></a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook utvecklare]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-portalen]: https://portal.azure.com/
