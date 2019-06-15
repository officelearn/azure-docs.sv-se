---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133547"
---
## <a name="register-your-application"></a>Registrera ditt program

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Om ditt konto får du tillgång till fler än en klient, väljer du kontot längst upp till höger och ange sedan sessionen portalen till Azure AD-klient som du vill använda.
1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan.
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. Under den **omdirigerings-URI** , i listrutan, väljer du den **Web** plattform och ange sedan värdet till programmets URL som baseras på webbservern. 

   Information om att och hämta omdirigerings-URL i Visual Studio och Node.js finns i följande två avsnitt.

1. Välj **Registrera**.
1. I appen **översikt** , Lägg den **(klient)-ID: T** värdet för senare användning.
1. Den här snabbstarten kräver att [flödet för implicit beviljande](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) aktiveras. I den vänstra rutan i det registrerade programmet, väljer **autentisering**.
1. I **avancerade inställningar**under **Implicit beviljande**väljer den **ID-token** och **åtkomsttoken** kryssrutorna. ID-token och åtkomsttoken krävs, eftersom den här appen behöver logga in användare och anropa ett API.
1. Välj **Spara**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Ange en omdirigerings-URL för Node.js
> För Node.js, du kan ange webb-serverport i den *server.js* fil. Den här självstudien använder port 30662 referens, men du kan använda någon annan port. 
>
> Om du vill konfigurera en omdirigerings-URL i programmet registreringsinformation växla tillbaka till den **programregistrering** fönstret och gör något av följande:
>
> - Ange *`http://localhost:30662/`* som den **omdirigerings-URL**.
> - Om du använder en anpassad TCP-port, använda *`http://localhost:<port>/`* (där  *\<port >* är anpassade TCP-portnumret).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Ange en omdirigerings-URL för Visual Studio
> Du kan hämta omdirigerings-URL för Visual Studio genom att göra följande:
> 1. I **Solution Explorer**, väljer du projektet.
>
>    Den **egenskaper** öppnas. Om den inte öppnas, trycker du på **F4**.
>
>    ![Fönstret JavaScriptSPA Projektegenskaper](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Kopiera den **URL** värde.
 
> 1. Gå tillbaka till den **programregistrering** fönstret och klistra in det kopierade värdet som den **omdirigerings-URL**.

#### <a name="configure-your-javascript-spa"></a>Konfigurera din JavaScript SPA

1. I den *index.html* filen som du skapade under installationen av projektet, Lägg till registreringsinformationen som programmet. Överst i filen, inom den `<script></script>` taggar, Lägg till följande kod:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Där:
    - *\<Enter_the_Application_Id_here >* är den **(klient)-ID: T** för programmet som du har registrerat.
    - *\<Enter_the_Tenant_info_here >* är inställd på något av följande alternativ:
       - Om ditt program stöder *konton i den här organisationskatalog*, Ersätt detta värde med den **klient-ID** eller **klientnamn** (till exempel  *Contoso.microsoft.com*).
       - Om ditt program stöder *konton i alla organisationskatalog*, Ersätt detta värde med **organisationer**.
       - Om ditt program stöder *konton alla organisationskatalog och personliga Microsoft-konton*, Ersätt detta värde med **vanliga**. Att begränsa stödet till *personliga Microsoft-konton endast*, Ersätt detta värde med **konsumenter**.
