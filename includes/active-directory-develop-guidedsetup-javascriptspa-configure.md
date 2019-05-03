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
ms.openlocfilehash: 387adcdf8bdabf90bc1e691a7a8ec9ae0a8e90dc
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "64993338"
---
## <a name="register-your-application"></a>Registrera ditt program

1. Logga in på [Azure-portalen](https://portal.azure.com/) för att registrera ett program.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan.
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. Under den **omdirigerings-URI** väljer den **Web** plattform och ange värdet till programmets URL baserat på din webbserver. Se avsnitten nedan för instruktioner om hur du anger och hämtar omdirigerings-URL i Visual Studio och nod.
1. När det är klart väljer du **Registrera**.
1. På appens sida **Översikt** antecknar du värdet för **Application (client) ID** (Program-ID (klient)).
1. Den här snabbstarten kräver att [flödet för implicit beviljande](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) aktiveras. I det vänstra navigeringsfönstret för det registrerade programmet väljer du **Autentisering**.
1. I **Avancerade inställningar** går du till **Implicit beviljande** och aktiverar kryssrutorna **ID-token** och **Åtkomsttoken**. ID-token och åtkomsttoken krävs eftersom den här appen behöver logga in användare och anropa ett API.
1. Välj **Spara**.

> #### <a name="setting-the-redirect-url-for-node"></a>Ange omdirigerings-URL för nod
> För Node.js, du kan ange webb-serverport i den *server.js* fil. Den här självstudien används port 30662 referens, men du kan använda någon annan port. Följ anvisningarna nedan för att ställa in en omdirigerings-URL i registreringsinformation för programmet:<br/>
> - Gå tillbaka till den *programregistrering* och ange `http://localhost:30662/` som en `Redirect URL`, eller Använd `http://localhost:[port]/` om du använder en anpassad TCP-port (där *[port]* är anpassade TCP-portnumret).

<p>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio-instruktionerna för att hämta omdirigerings-URL
> Följ dessa steg för att hämta omdirigerings-URL:
> 1. I **Solution Explorer**, väljer du projektet och titta på den **egenskaper** fönster. Om du inte ser en **egenskaper** fönster, tryck på **F4**.
> 2. Kopiera värdet från **URL** till Urklipp:<br/> ![Projektegenskaper](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Gå tillbaka till den *programregistrering* och ange värdet som en **omdirigerings-URL**.

#### <a name="configure-your-javascript-spa"></a>Konfigurera din JavaScript SPA

1. I den `index.html` filen som skapades under projektkonfiguration, lägga till registreringsinformationen som programmet. Lägg till följande kod högst upp i den `<script></script>` taggar i brödtexten i din `index.html` fil:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here",
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Där:
    - `Enter_the_Application_Id_here` – är **program-ID (klient)** för programmet som du har registrerat.
    - `Enter_the_Tenant_Info_Here` – är inställt på något av följande alternativ:
       - Om ditt program stöder **konton i den här organisationskatalog**, Ersätt detta värde med den **klient-ID** eller **klientnamn** (t.ex. Contoso.microsoft.com)
       - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
       - Om ditt program stöder **konton alla organisationskatalog och personliga Microsoft-konton**, Ersätt detta värde med `common`. Att begränsa stödet till *personliga Microsoft-konton endast*, Ersätt detta värde med `consumers`.
