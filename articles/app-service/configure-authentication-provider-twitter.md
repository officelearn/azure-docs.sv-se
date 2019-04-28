---
title: Konfigurera Twitter - autentisering i Azure App Service
description: Lär dig hur du konfigurerar Twitter-autentisering för App Services-programmet.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 51a2ac93fd2d863855c820ba147418c5397c2a89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851553"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Så här konfigurerar du App Service-programmet att använda Twitter-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Twitter som en autentiseringsprovider.

Du måste ha ett Twitter-konto som har en verifierad e-postadress och telefonnummer nummer för att slutföra den här proceduren i det här avsnittet. Om du vill skapa ett nytt Twitter-konto går du till <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registrera ditt program med Twitter
1. Logga in på den [Azure Portal], och navigera till ditt program. Kopiera din **URL**. Du kommer används för att konfigurera din Twitter-app.
2. Navigera till den [Twitter-utvecklare] webbplats, logga in med dina autentiseringsuppgifter för Twitter-konto och klicka på **Skapa ny App**.
3. Ange den **namn** och en **beskrivning** för din nya app. Klistra in i ditt program **URL** för den **webbplats** värde. Sedan för den **Motringnings-URL**, klistra in den **Motringnings-URL för** du kopierade tidigare. Här hittar du Mobilapp läggas till med sökvägen */.auth/login/twitter/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Kontrollera att du använder HTTPS-schema.
4. Längst ned på sidan, Läs och acceptera villkoren. Klicka sedan på **skapa ditt Twitter-program**. Detta registrerar appen Visar programinformation.
5. Klicka på den **inställningar** fliken, kontrollera **gör så att programmet som ska användas för att logga in med Twitter**, klicka sedan på **uppdateringsinställningar**.
6. Välj den **nycklar och åtkomsttoken** fliken. Anteckna värdena för **använda nyckeln (API-nyckel)** och **konsumenthemligheten (API-hemlighet)**.
   
   > [!NOTE]
   > Konsumenthemligheten är en viktig säkerhetsuppgift för autentisering. Inte dela den här hemligheten med vem som helst och distribuera den med din app.
   > 
   > 

## <a name="secrets"> </a>Lägga till Twitter-information i ditt program
1. I den [Azure Portal], navigera till ditt program. Klicka på **inställningar**, och sedan **autentisering / auktorisering**.
2. Om autentisering / auktorisering är inte aktiverad, aktivera växeln **på**.
3. Klicka på **Twitter**. Klistra in App-ID och Apphemlighet värden som du hämtade tidigare. Klicka sedan på **OK**.
   
   ![][1]
   
   Som standard, App Service ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din kod.
4. (Valfritt) Om du vill begränsa åtkomsten till din plats för att endast användare som autentiseras av Twitter, ange **åtgärd att vidta när begäran inte har autentiserats** till **Twitter**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Twitter för autentisering.
5. Klicka på **Spara**.

Du är nu redo att använda Twitter för autentisering i din app.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter-utvecklare]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
