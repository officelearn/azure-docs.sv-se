---
title: "Hur du konfigurerar Google-autentisering för tillämpningsprogrammet Apptjänster"
description: "Lär dig hur du konfigurerar Google-autentisering för tillämpningsprogrammet Apptjänster."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: d6c1707f67d986487e5a45e76ffc9a02ddf16eb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Så här konfigurerar du din App tjänstprogram att använda Google-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Google som en autentiseringsprovider.

Du måste ha ett Google-konto som har en verifierad e-postadress för att slutföra proceduren i det här avsnittet. Gå till [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302) om du vill skapa ett nytt Google-konto.

## <a name="register"></a>Registrera programmet med Google
1. Logga in på den [Azure-portalen], och navigera till programmet. Kopiera ditt **URL**, som du senare använda för att konfigurera din app på Google.
2. Navigera till den [Google API: er](http://go.microsoft.com/fwlink/p/?LinkId=268303) logga in med autentiseringsuppgifterna för ditt Google-konto, klickar du på **skapa projekt**, ange en **projektnamn**, klicka på  **Skapa**.
3. Under **sociala API: er** klickar du på **Google + API** och sedan **aktivera**.
4. I det vänstra navigeringsfönstret **autentiseringsuppgifter** > **OAuth-medgivande skärmen**och välj din **e-postadress**, ange en **produktnamn**, och klicka på **spara**.
5. I den **autentiseringsuppgifter** klickar du på **skapa autentiseringsuppgifter** > **OAuth-klient-ID**och välj **webbprogrammet**.
6. Klistra in App Service **URL** du kopierade tidigare i **behörighet JavaScript ursprung**, klistra sedan in din omdirigering URI i **behörighet omdirigerings-URI**. Omdirigerings-URI: N är URL-Adressen till ditt program läggas till med sökvägen */.auth/login/google/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/google/callback`. Kontrollera att du använder HTTPS-schema. Klicka sedan på **Skapa**.
7. Anteckna värdena för klient-ID och klienthemlighet på nästa skärm.

    > [!IMPORTANT]
    > Klienthemligheten är en viktig säkerhetsuppgift för autentisering. Dela den här hemligheten med någon eller inte distribuera inom ett klientprogram.


## <a name="secrets"></a>Lägga till Google information i programmet
1. I den [Azure-portalen], navigera till programmet. Klicka på **inställningar**, och sedan **autentisering / auktorisering**.
2. Om autentisering / auktorisering är inte aktiverad, aktivera växeln **på**.
3. Klicka på **Google**. Klistra in i appen hemligheten och App-ID-värden som du hämtade tidigare och välja att aktivera alla scope som krävs för ditt program. Klicka sedan på **OK**.
   
   ![][1]
   
   Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod.
4. (Valfritt) Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiseras av Google, ange **åtgärd att vidta när begäran inte har autentiserats** till **Google**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Google för autentisering.
5. Klicka på **Spara**.

Du är nu redo att använda Google för autentisering i appen.

## <a name="related-content"></a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portalen]: https://portal.azure.com/

