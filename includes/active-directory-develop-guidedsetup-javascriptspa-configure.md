---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 135ee9f6b833165cd393b9c5ca582e0ee9499e0f
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057424"
---
## <a name="register-your-application"></a>Registrera ditt program

1. Logga in på den [Azure-portalen](https://portal.azure.com/) att registrera ett program.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion) > Ny registrering**.
1. När den **registrera ett program** visas, ange ett namn för ditt program.
1. Under **stöds kontotyper**väljer **konton alla organisationskatalog och personliga Microsoft-konton**.
1. Under den **omdirigerings-URI** väljer den **Web** plattform och ange värdet till programmets URL baserat på din webbserver. Se avsnitten nedan för instruktioner om hur du anger och hämtar omdirigerings-URL i Visual Studio och nod.
1. När det är klart väljer du **Registrera**.
1. I appen **översikt** sidan, Skriv ned den **(klient)-ID: T** värde.
1. Den här snabbstarten krävs det [flöde beviljat med Implicit](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md) aktiveras. I det vänstra navigeringsfönstret för det registrerade programmet, väljer **autentisering**.
1. I **avancerade inställningar**under **Implicit beviljande**, aktivera båda **ID-token** och **åtkomsttoken** kryssrutorna. ID-token och åtkomsttoken krävs eftersom den här appen måste du logga in användare och anropa ett API.
1. Välj **Spara**.

> #### <a name="setting-the-redirect-url-for-node"></a>Ange omdirigerings-URL för nod
> För Node.js, du kan ange webb-serverport i den *server.js* fil. Den här självstudien används port 30662 referens, men du kan använda någon annan port. Följ anvisningarna nedan för att ställa in en omdirigerings-URL i registreringsinformation för programmet:<br/>
> - Gå tillbaka till den *programregistrering* och ange `http://localhost:30662/` som en `Redirect URL`, eller Använd `http://localhost:[port]/` om du använder en anpassad TCP-port (där *[port]* är anpassade TCP-portnumret).

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio-instruktionerna för att hämta omdirigerings-URL
> Följ dessa steg för att hämta omdirigerings-URL:
> 1. I **Solution Explorer**, väljer du projektet och titta på den **egenskaper** fönster. Om du inte ser en **egenskaper** fönster, tryck på **F4**.
> 2. Kopiera värdet från **URL** till Urklipp:<br/> ![Projektegenskaper](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Gå tillbaka till den *programregistrering* och ange värdet som en **omdirigerings-URL**.

#### <a name="configure-your-javascript-spa"></a>Konfigurera din JavaScript SPA

1. I den `index.html` filen som skapades under projektkonfiguration, lägga till registreringsinformationen som programmet. Lägg till följande kod högst upp i den `<script></script>` taggar i brödtexten i din `index.html` fil:

    ```javascript
    var applicationConfig = {
        clientID: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/common",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Ersätt <code>Enter the application Id here</code> med program-ID som du just registrerade.
</li>
</ol>
