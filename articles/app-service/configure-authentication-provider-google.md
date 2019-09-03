---
title: Konfigurera Google-autentisering – Azure App Service
description: Lär dig hur du konfigurerar Google-autentisering för din App Service-app.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232145"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Så här konfigurerar du ditt App Service program så att Google-inloggning används
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

I det här avsnittet visas hur du konfigurerar Azure App Service att använda Google som en autentiseringsprovider.

För att slutföra proceduren i det här avsnittet måste du ha ett Google-konto som har en verifierad e-postadress. Gå till [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) om du vill skapa ett nytt Google-konto.

## <a name="register"> </a>Registrera ditt program med Google
1. Följ Google-dokumentationen på [Google-inloggningen för appar på Server sidan](https://developers.google.com/identity/sign-in/web/server-side-flow) för att skapa ett klient-ID och klient hemlighet, med följande information (du behöver inte göra några kod ändringar):
    - För **behöriga JavaScript-ursprung**använder `https://<app-name>.azurewebsites.net` du med namnet på din app i  *\<App-Name->* .
    - Använd`https://<app-name>.azurewebsites.net/.auth/login/google/callback`för auktoriserad omdirigerings- **URI**.
1. När klient-ID och klient hemligheter har skapats kopierar du deras värden.

    > [!IMPORTANT]
    > Klient hemligheten är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.


## <a name="secrets"> </a>Lägga till Google information till ditt program
1. Gå till din App Service-app i [Azure Portal]. Välj **autentisering/auktorisering**på den vänstra menyn.
2. Om funktionen autentisering/auktorisering inte är aktive rad aktiverar du växeln till **på**.
3. Klicka på **Google**. Klistra in i app-ID: t och de hemliga värdena för appen som du har fått tidigare och eventuellt aktivera alla omfattningar som programmet kräver. Klicka sedan på **OK**.

   App Service tillhandahåller autentisering men begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Mer information finns i [auktorisera eller neka användare](app-service-authentication-how-to.md#authorize-or-deny-users).
4. Valfritt Om du vill begränsa åtkomsten till din webbplats till endast användare som autentiserats av Google, anger **du åtgärd som ska vidtas när begäran inte autentiseras** till **Google**. Detta kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Google för autentisering.

    > [!NOTE]
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

