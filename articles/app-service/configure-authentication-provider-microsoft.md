---
title: Konfigurera Microsoft-autentisering
description: Lär dig hur du konfigurerar Microsoft-kontoautentisering som identitetsleverantör för apptjänstappen.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 95c603d4a10eb0e4d0817e20755c0f9b36baa96f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842341"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Konfigurera appen App Service så att den använder Microsoft Account-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service för att använda AAD för att stödja personliga Microsoft-kontoinloggningar.

> [!NOTE]
> Både personliga Microsoft-konton och organisationskonton använder AAD-identitetsprovidern. För närvarande är det inte möjligt att konfigurera den här identitetsprovidern för att stödja båda typerna av inloggningar.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registrera din app med Microsoft-konto

1. Gå till [**Appregistreringar**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure-portalen. Logga in med ditt Microsoft-konto om det behövs.
1. Välj **Ny registrering**och ange sedan ett programnamn.
1. Under **Kontotyper som stöds**väljer du Konton i valfri **organisationskatalog (Alla Azure AD-kataloger – Multitenant) och personliga Microsoft-konton (t.ex.**
1. I **Omdirigera URI:er**väljer `https://<app-domain-name>/.auth/login/aad/callback`du **Webb**och anger sedan . Ersätt * \<app-domännamn>* med appens domännamn.  Till exempel `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Var noga med att använda HTTPS-schemat i URL:en.

1. Välj **Registrera**.
1. Kopiera **programmets (klient)-ID**. Du behöver det senare.
1. Välj Certifikat & **hemligheter** > **Ny klienthemlighet**i den vänstra rutan. Ange en beskrivning, välj giltighetstid och välj **Lägg till**.
1. Kopiera värdet som visas på sidan **Certifikat & hemligheter.** När du har lämnat sidan visas den inte igen.

    > [!IMPORTANT]
    > Klientens hemliga värde (lösenord) är en viktig säkerhetsautentiseringsinformation. Dela inte lösenordet med någon eller distribuera det i ett klientprogram.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Lägga till Microsoft-kontoinformation i apptjänstprogrammet

1. Gå till ditt program i [Azure-portalen].
1. Välj **Inställningar** > **Autentisering /Auktorisering**och kontrollera att **apptjänstautentiseringen** är **på**.
1. Under **Autentiseringsleverantörer**väljer du **Azure Active Directory**. Välj **Avancerat** under **Hanteringsläge**. Klistra in i program-ID:t och klienthemligheten som du fick tidigare. Används **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** för fältet **Utfärdare url.**
1. Välj **OK**.

   AppTjänsten tillhandahåller autentisering, men begränsar inte den behöriga åtkomsten till webbplatsens innehåll och API:er. Du måste auktorisera användare i din appkod.

1. (Valfritt) Om du vill begränsa åtkomsten till Microsoft-kontoanvändare anger du **Åtgärd som ska vidtas när begäran inte autentiseras** **logga in med Azure Active Directory**. När du ställer in den här funktionen kräver appen att alla begäranden ska autentiseras. Det omdirigerar också alla oautentiserade begäranden att använda AAD för autentisering. Observera att eftersom du har konfigurerat **utfärdarens url** för att använda Microsoft-kontoklientinnehavaren autentiseras endast personliga acccounts.

   > [!CAUTION]
   > Att begränsa åtkomsten på det här sättet gäller alla samtal till din app, vilket kanske inte är önskvärt för appar som har en allmänt tillgänglig startsida, som i många ensidiga program. För sådana program kan **tillåt anonyma begäranden (ingen åtgärd)** att föredra så att appen startar själva autentiseringen manuellt. Mer information finns i [Autentiseringsflöde](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Microsoft-konto för autentisering i din app.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portal]: https://portal.azure.com/
