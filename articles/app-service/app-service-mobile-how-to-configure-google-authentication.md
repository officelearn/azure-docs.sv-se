---
title: Så här konfigurerar du Google-autentisering för ditt program för App Services
description: Lär dig mer om att konfigurera Google-autentisering för App Services-appen.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 35e02defb8b28c60c70c8b50c28bba8499392377
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961546"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Så här konfigurerar du App Service-programmet att använda Google-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Google som en autentiseringsprovider.

Du måste ha ett Google-konto som har en verifierad e-postadress för att slutföra den här proceduren i det här avsnittet. Gå till [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) om du vill skapa ett nytt Google-konto.

## <a name="register"> </a>Registrera ditt program med Google
1. Logga in på den [Azure Portal], och navigera till ditt program. Kopiera din **URL**, som du senare använda för att konfigurera din Google-app.
2. Navigera till den [Google API: er](https://go.microsoft.com/fwlink/p/?LinkId=268303) webbplats, logga in med autentiseringsuppgifterna för ditt Google-konto, klickar du på **skapa projekt**, ange en **projektnamn**, klicka sedan på  **Skapa**.
3. När projektet har skapats kan du välja den. Från instrumentpanelen för projektet klickar du på **går du till API: er översikt**.
4. Välj **aktivera API: er och tjänster**. Sök efter **Google + API**, och markera den. Klicka sedan på **aktivera**.
5. I det vänstra navigeringsfönstret **autentiseringsuppgifter** > **OAuth godkännandeskärmen**och välj sedan din **e-postadress**, ange en **produktnamn**, och klicka på **spara**.
6. I den **autentiseringsuppgifter** fliken **skapa autentiseringsuppgifter** > **OAuth klient-ID**.
7. På skärmen ”skapa klient-ID” Välj **webbprogram**.
8. Klistra in App Service- **URL** du kopierade tidigare i **behörighet JavaScript ursprung**, klistra sedan in din omdirigerings-URI i **behörighet omdirigerings-URI**. Omdirigerings-URI: N är URL: en för ditt program läggas till med sökvägen */.auth/login/google/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/google/callback`. Kontrollera att du använder HTTPS-schema. Klicka sedan på **Skapa**.
9. Anteckna värdena för klient-ID och klienthemlighet på nästa skärm.

    > [!IMPORTANT]
    > Klienthemlighet är en viktig säkerhetsuppgift för autentisering. Inte dela den här hemligheten med vem som helst och distribuera det i ett klientprogram.


## <a name="secrets"> </a>Lägga till Google-information i ditt program
1. I den [Azure Portal], navigera till ditt program. Klicka på **inställningar**, och sedan **autentisering / auktorisering**.
2. Om autentisering / auktorisering är inte aktiverad, aktivera växeln **på**.
3. Klicka på **Google**. Klistra in i App-ID och en Apphemlighet värden som du hämtade tidigare och välja att eventuella scope som krävs för ditt program. Klicka sedan på **OK**.
   
   ![][1]
   
   Som standard, App Service ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din kod.
4. (Valfritt) Om du vill begränsa åtkomsten till din plats för att endast användare som autentiseras av Google, ange **åtgärd att vidta när begäran inte har autentiserats** till **Google**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Google för autentisering.
5. Klicka på **Spara**.

Du är nu redo att använda Google för autentisering i din app.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

