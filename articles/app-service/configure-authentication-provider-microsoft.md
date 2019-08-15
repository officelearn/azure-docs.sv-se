---
title: Konfigurera autentisering av Microsoft-konto – Azure App Service
description: Lär dig hur du konfigurerar autentisering med Microsoft-konto för ditt App Services-program.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 729c235ee51a904714358221389703632fd8296c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033634"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Så här konfigurerar du ditt App Service program för att använda inloggning med Microsoft-konto
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Det här avsnittet visar hur du konfigurerar Azure App Service att använda Microsoft-konto som autentiseringsprovider. 

## <a name="register-microsoft-account"> </a>Registrera din app med Microsoft-konto
1. Logga in på [Azure Portal]och navigera till ditt program. 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. Gå till [**Appregistreringar**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)och logga in med ditt Microsoft-konto, om det behövs.

1. Klicka på **ny registrering**och skriv sedan ett program namn.

1. I omdirigerings- **URI: er**väljer du `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application` **webb**och skriver sedan. *Ersätt\<app-Domain-Name >* med domän namnet för din app.  Till exempel `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. 

   > [!NOTE]
   > Använd HTTPS-schemat i URL: en.

1. Välj **register**. 

1. Kopiera **program-ID: t (klient)** . Du behöver den senare. 
   
7. Välj **certifikat & hemligheter** > **ny klient hemlighet**i det vänstra navigerings fönstret i den nya appens registrering. Ange en beskrivning, Välj giltighets tid och välj **Lägg till**.

1. Kopiera värdet som visas på sidan **certifikat & hemligheter** . När du lämnar sidan visas den inte igen.

    > [!IMPORTANT]
    > Lösen ordet är en viktig säkerhets autentiseringsuppgift. Dela inte lösen ordet med någon eller distribuera det i ett klient program.

## <a name="secrets"> </a>Lägg till information om Microsoft-konto till ditt App Service program
1. I [Azure Portal]navigerar du till ditt program. Klicka på **autentisering/auktorisering**i det vänstra navigerings fältet.

2. Om funktionen autentisering/auktorisering inte är aktive rad väljer du **på**.

3. Under **autentiseringsproviders**väljer du **Microsoft-konto**. Klistra in det program (klient)-ID och klient hemlighet som du fick tidigare och om du vill aktivera alla omfattningar som programmet kräver. Klicka sedan på **OK**.

    Som standard tillhandahåller App Service autentisering men begränsar inte tillåten åtkomst till ditt webbplats innehåll och API: er. Du måste auktorisera användare i din app-kod.

4. Valfritt Om du vill begränsa åtkomsten till Microsoft-konto användare anger du **åtgärd som ska vidtas när begäran inte autentiseras** att **Logga in med Microsoft-konto**. Detta kräver att alla begär Anden autentiseras och att alla oautentiserade begär Anden omdirigeras till Microsoft-konto för autentisering.

> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program kan du **tillåta anonyma begär Anden (ingen åtgärd)** , med appen manuellt startar inloggningen, enligt beskrivningen [här](overview-authentication-authorization.md#authentication-flow).

5. Klicka på **Spara**.

Du är nu redo att använda Microsoft-kontot för autentisering i din app.

## <a name="related-content"> </a>Relaterat innehåll
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
