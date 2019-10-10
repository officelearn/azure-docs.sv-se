---
title: Konfigurera autentisering av Microsoft-konto – Azure App Service
description: Lär dig hur du konfigurerar autentisering med Microsoft-konto för din App Service-app.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 70af534e6bcd0039dbc602a5ebc3fc35fb145e79
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176936"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Konfigurera din App Service-app att använda inloggning med Microsoft-konto

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service att använda Microsoft-konto som autentiseringsprovider. 

## <a name="register-microsoft-account"> </a>Registrera din app med Microsoft-konto

1. Gå till [**Appregistreringar**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Portal. Om det behövs loggar du in med din Microsoft-konto.
1. Välj **ny registrering**och ange sedan ett program namn.
1. I **omdirigerings-URI: er**väljer du **webb**och anger sedan `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`. Ersätt *\<APP-Domain-name >* med domän namnet för din app.  Till exempel `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Se till att använda HTTPS-schemat i URL: en.

1. Välj **Registrera**.
1. Kopiera **program-ID: t (klient)** . Du behöver det senare.
1. I den vänstra rutan väljer du **certifikat & hemligheter** > **ny klient hemlighet**. Ange en beskrivning, Välj giltighets tid och välj **Lägg till**.
1. Kopiera värdet som visas på sidan **certifikat & hemligheter** . När du har lämnat sidan visas den inte igen.

    > [!IMPORTANT]
    > Lösen ordet är en viktig säkerhets autentiseringsuppgift. Dela inte lösen ordet med någon eller distribuera det i ett klient program.

## <a name="secrets"> </a>Lägg till information om Microsoft-konto till ditt App Service program

1. Gå till ditt program i [Azure-portalen].
1. Välj **inställningar** > **autentisering/auktorisering**och se till att **App Service autentisering** är **aktiverat**.
1. Under **autentiseringsproviders**väljer du **Microsoft-konto**. Klistra in det program (klient)-ID och klient hemlighet som du fick tidigare. Aktivera alla omfattningar som krävs av ditt program.
1. Välj **OK**.

   App Service tillhandahåller autentisering, men begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din app-kod.

1. Valfritt Om du vill begränsa åtkomsten till Microsoft-konto användare anger du **åtgärd som ska vidtas när begäran inte autentiseras** att **Logga in med Microsoft-konto**. När du ställer in den här funktionen kräver appen att alla begär Anden ska autentiseras. Det omdirigerar också alla oautentiserade begär anden till Microsoft-konto för autentisering.

   > [!CAUTION]
   > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som har en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program **tillåts anonyma begär Anden (ingen åtgärd)** , så att appen manuellt startar själva autentiseringen. Mer information finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).

1. Välj **Spara**.

Du är nu redo att använda Microsoft-kontot för autentisering i din app.

## <a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-portalen]: https://portal.azure.com/
