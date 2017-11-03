---
title: "Hur du konfigurerar Twitter-autentisering för tillämpningsprogrammet Apptjänster"
description: "Lär dig hur du konfigurerar Twitter-autentisering för tillämpningsprogrammet Apptjänster."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: afde020b7817dc58ecea24eb4a09cf93d0986eb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Så här konfigurerar du din App tjänstprogram att använda Twitter-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Twitter som en autentiseringsprovider.

Du måste ha ett Twitter-konto som har ett verifierad e-postadressen och telefonnumret om du vill slutföra proceduren i det här avsnittet. Om du vill skapa ett nytt Twitter-konto går du till <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"></a>Registrera programmet med Twitter
1. Logga in på den [Azure-portalen], och navigera till programmet. Kopiera ditt **URL**. Du använder detta för att konfigurera din app på Twitter.
2. Navigera till den [Twitter utvecklare] webbplats, logga in med dina autentiseringsuppgifter för Twitter-konto och klicka på **Skapa ny App**.
3. Ange den **namn** och en **beskrivning** för din nya app. Klistra in i ditt program **URL** för den **webbplats** värde. Sedan för den **motringning URL**, klistra in den **motringning URL** du kopierade tidigare. Här hittar du Mobilapp läggas till med sökvägen */.auth/login/twitter/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Kontrollera att du använder HTTPS-schema.
4. Läs och acceptera villkoren längst ned på sidan. Klicka på **skapa programmet Twitter**. Detta registrerar appen visas programinformation.
5. Klicka på den **inställningar** markerar **att det här tillämpningsprogrammet som används för att logga in med Twitter**, klicka på **uppdateringsinställningar**.
6. Välj den **nycklar och åtkomst-token** fliken. Anteckna värdena för **konsumentnyckel (API-nyckel)** och **konsumenten hemligheten (API hemliga)**.
   
   > [!NOTE]
   > Hemligheten som konsumenten är en viktig säkerhetsuppgift för autentisering. Dela den här hemligheten med någon eller inte distribuera med din app.
   > 
   > 

## <a name="secrets"></a>Twitter-Lägg till information i programmet
1. I den [Azure-portalen], navigera till programmet. Klicka på **inställningar**, och sedan **autentisering / auktorisering**.
2. Om autentisering / auktorisering är inte aktiverad, aktivera växeln **på**.
3. Klicka på **Twitter**. Klistra in i App-ID och App hemlighet värden som du hämtade tidigare. Klicka sedan på **OK**.
   
   ![][1]
   
   Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod.
4. (Valfritt) Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiseras av Twitter, ange **åtgärd att vidta när begäran inte har autentiserats** till **Twitter**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Twitter för autentisering.
5. Klicka på **Spara**.

Du är nu redo att använda Twitter för autentisering i appen.

## <a name="related-content"></a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter utvecklare]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure-portalen]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
