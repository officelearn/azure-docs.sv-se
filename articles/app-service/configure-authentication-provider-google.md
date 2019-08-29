---
title: Konfigurera Google-autentisering – Azure App Service
description: Lär dig hur du konfigurerar Google-autentisering för ditt App Services-program.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 4e28f4e330fa24476b717334dfc6d3265640c62a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088214"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Så här konfigurerar du ditt App Service program så att Google-inloggning används
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

I det här avsnittet visas hur du konfigurerar Azure App Service att använda Google som en autentiseringsprovider.

För att slutföra proceduren i det här avsnittet måste du ha ett Google-konto som har en verifierad e-postadress. Gå till [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) om du vill skapa ett nytt Google-konto.

## <a name="register"> </a>Registrera ditt program med Google
1. Logga in på [Azure Portal]och navigera till ditt program. Kopiera din **URL**, som du senare använder för att konfigurera Google-appen.
2. Gå till [Google API](https://go.microsoft.com/fwlink/p/?LinkId=268303) -webbplatsen, logga in med dina Google-kontoautentiseringsuppgifter, klicka **på skapa projekt**, ange ett **projekt namn**och klicka sedan på **skapa**.
3. När projektet har skapats väljer du det. I instrument panelen för projektet klickar **du på gå till API: översikt**.
4. Välj **Aktivera API: er och tjänster**. Sök efter **Google + API**och markera det. Klicka sedan på **Aktivera**.
5. I det vänstra navigerings fönstret, anger **autentiseringsuppgifter** > **OAuth-medgivande**, välj din **e-postadress**, ange ett **produkt namn**och klicka på **Spara**.
6. På fliken **autentiseringsuppgifter** klickar du på **skapa autentiseringsuppgifter** > **OAuth-klient-ID**.
7. På skärmen "skapa klient-ID" väljer du **webb program**.
8. Klistra in App Service- **URL** som du kopierade tidigare till **behöriga JavaScript-ursprung**och klistra sedan in OMdirigerings-URI: n i auktoriserad omdirigering Omdirigerings-URI: n är URL: en för programmet som lagts till med sökvägen */.auth/login/Google/callback*. Till exempel `https://contoso.azurewebsites.net/.auth/login/google/callback`. Kontrol lera att du använder HTTPS-schemat. Klicka sedan på **Skapa**.
9. Anteckna värdena för klient-ID och klient hemlighet på nästa skärm.

    > [!IMPORTANT]
    > Klient hemligheten är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.


## <a name="secrets"> </a>Lägga till Google information till ditt program
1. Gå tillbaka till ditt program i [Azure Portal]. Klicka på **Inställningar**och sedan **autentisering/auktorisering**.
2. Om funktionen autentisering/auktorisering inte är aktive rad aktiverar du växeln till **på**.
3. Klicka på **Google**. Klistra in i app-ID: t och de hemliga värdena för appen som du har fått tidigare och eventuellt aktivera alla omfattningar som programmet kräver. Klicka sedan på **OK**.
   
   ![][1]
   
   Som standard tillhandahåller App Service autentisering men begränsar inte tillåten åtkomst till ditt webbplats innehåll och API: er. Du måste auktorisera användare i din app-kod.
4. Valfritt Om du vill begränsa åtkomsten till din webbplats till endast användare som autentiserats av Google, anger **du åtgärd som ska vidtas när begäran inte autentiseras** till **Google**. Detta kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Google för autentisering.

> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program kan du **tillåta anonyma begär Anden (ingen åtgärd)** , med appen manuellt startar inloggningen, enligt beskrivningen [här](overview-authentication-authorization.md#authentication-flow).

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

