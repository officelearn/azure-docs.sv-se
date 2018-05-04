---
title: Hur du konfigurerar Google-autentisering för tillämpningsprogrammet Apptjänster
description: Lär dig hur du konfigurerar Google-autentisering för tillämpningsprogrammet Apptjänster.
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
ms.openlocfilehash: 1a174913446c0a1d5e3e3b01123db8b40bfd172c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Så här konfigurerar du din App tjänstprogram att använda Google-inloggning
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda Google som en autentiseringsprovider.

Du måste ha ett Google-konto som har en verifierad e-postadress för att slutföra proceduren i det här avsnittet. Gå till [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302) om du vill skapa ett nytt Google-konto.

## <a name="register"> </a>Registrera ditt program med Google
1. Logga in på den [Azure-portalen], och navigera till programmet. Kopiera ditt **URL**, som du senare använda för att konfigurera din app på Google.
2. Navigera till den [Google API: er](http://go.microsoft.com/fwlink/p/?LinkId=268303) logga in med autentiseringsuppgifterna för ditt Google-konto, klickar du på **skapa projekt**, ange en **projektnamn**, klicka på  **Skapa**.
3. När projektet har skapats, väljer du den. Från instrumentpanelen för projektet klickar du på **gå till översikt över API: er**.
4. Välj **aktivera API: er och tjänster**. Sök efter **Google + API**, och markera den. Klicka på **aktivera**.
6. I det vänstra navigeringsfönstret **autentiseringsuppgifter** > **OAuth-medgivande skärmen**och välj din **e-postadress**, ange en **produktnamn**, och klicka på **spara**.
7. I den **autentiseringsuppgifter** klickar du på **skapa autentiseringsuppgifter** > **OAuth-klient-ID**. Klicka på **konfigurera medgivande skärmen**, ange en **produktnamn**. Klicka på **spara**
8. Välj ”Skapa klient-ID”-skärmen **webbprogrammet**.
9. Klistra in App Service **URL** du kopierade tidigare i **behörighet JavaScript ursprung**, klistra sedan in din omdirigering URI i **behörighet omdirigerings-URI**. Omdirigerings-URI: N är URL-Adressen till ditt program läggas till med sökvägen */.auth/login/google/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/google/callback`. Kontrollera att du använder HTTPS-schema. Klicka sedan på **Skapa**.
10. Anteckna värdena för klient-ID och klienthemlighet på nästa skärm.

    > [!IMPORTANT]
    > Klienthemligheten är en viktig säkerhetsuppgift för autentisering. Dela den här hemligheten med någon eller inte distribuera inom ett klientprogram.


## <a name="secrets"> </a>Lägga till Google information i ditt program
1. I den [Azure-portalen], navigera till programmet. Klicka på **inställningar**, och sedan **autentisering / auktorisering**.
2. Om autentisering / auktorisering är inte aktiverad, aktivera växeln **på**.
3. Klicka på **Google**. Klistra in i appen hemligheten och App-ID-värden som du hämtade tidigare och välja att aktivera alla scope som krävs för ditt program. Klicka sedan på **OK**.
   
   ![][1]
   
   Som standard Apptjänst ger autentisering, men begränsar inte auktoriserad åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din Appkod.
4. (Valfritt) Om du vill begränsa åtkomsten till din webbplats till enbart användare som autentiseras av Google, ange **åtgärd att vidta när begäran inte har autentiserats** till **Google**. Detta kräver att alla förfrågningar autentiseras och alla oautentiserade begäranden omdirigeras till Google för autentisering.
5. Klicka på **Spara**.

Du är nu redo att använda Google för autentisering i appen.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-portalen]: https://portal.azure.com/

