---
title: Logga in användare i Java Script-appar med en sida med auth-kod | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur en JavaScript-app kan anropa ett API som kräver åtkomsttoken med hjälp av Microsoft Identity Platform.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: d362db3a51848603c78d663c5b628192ff028d02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182155"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Snabb start: Logga in användare och hämta en åtkomsttoken i ett Java Script SPA med auth Code Flow 

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av den här funktionen kan ändras före allmän tillgänglighet (GA).


I den här snabb starten används MSAL. js 2,0 med Authorization Code Flow. Om du vill använda MSAL. js 1,0 med det implicita flödet kan du se [den här snabb](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript)starten.

I den här snabb starten använder du ett kod exempel för att lära dig hur ett Java Script-program (Single-Side Application) kan logga in användare av personliga konton, arbets konton och skol konton. En JavaScript-SPA kan också hämta en åtkomsttoken för att anropa Microsoft Graph-API: et eller något webb-API. Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en Azure-prenumeration kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (för att redigera projektfiler)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Starta snabb starts programmet med något av följande alternativ.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1 (Express): registrera och konfigurera appen automatiskt och ladda ned kod exemplet
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com).
> 1. Om ditt konto ger dig åtkomst till fler än en klient väljer du kontot längst upp till höger och anger sedan din portal-session till den Azure Active Directory (Azure AD) som du vill använda.
> 1. Välj [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Ange ett namn för ditt program.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Registrera**.
> 1. Gå till snabb starts fönstret och följ instruktionerna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2 (manuell): registrera och konfigurera ditt program och kod exempel manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com).
>
> 1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto längst upp till höger och anger sedan din portal-session till den Azure AD-klient som du vill använda.
> 1. Välj [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Registrera**. På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
> 1. I det vänstra fönstret i det registrerade programmet väljer du **autentisering**.
> 1. Under **plattforms konfiguration**väljer du **Lägg till en plattform**. En panel öppnas till vänster. Där väljer du region för **enskild sida** .
> 1. Fortfarande till vänster anger du **omdirigerings-URI** - `http://localhost:3000/`värdet till. 
> 1. Välj **Konfigurera**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> Om du vill att kod exemplet i den här snabb starten ska fungera måste du `redirectUri` lägga `http://localhost:3000/`till ett som.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

> [!div renderon="docs"]
> Om du vill köra projektet med en webb server med hjälp av Node. js [laddar du ned de centrala projektfilerna](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Köra projektet med en webb server med hjälp av Node. js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera din JavaScript-app
>
> Redigera *authConfig. js* `clientID`i mappen *app* och ange värdena `authority` och `redirectUri` i `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Där:
> - Enter_the_Application_Id_Here>är **program-ID: t (Client)** för det program som du har registrerat. * \<*
> - Enter_the_Cloud_Instance_Id_Here>är instansen av Azure-molnet. * \<* För det största eller globala Azure-molnet anger *https://login.microsoftonline.com/* du bara. För **nationella** moln (till exempel Kina), se [nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - Enter_the_Tenant_info_here>har angetts till något av följande alternativ: * \<*
>    - Om ditt program har stöd *för konton i den här organisations katalogen*ersätter du värdet med **klient-ID** eller **klient namn** (till exempel *contoso.Microsoft.com*).
>    - Om ditt program har stöd *för konton i en organisations katalog*ersätter du värdet med **organisationer**.
>    - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton*ersätter du värdet med **vanligt**. Om du bara vill begränsa stödet till *personliga Microsoft-konton*ersätter du värdet med **konsumenter**.
> - * \<Enter_the_Redirect_Uri_Here>* är`http://localhost:3000`
> > [!TIP]
> > Om du vill hitta värdena för **program-ID**, **katalog (klient)-ID**och **konto typer som stöds**, går du till registrerings sidan för **Overview** app-registrering i Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper.

> [!div renderon="docs"]
>
> Sedan, fortfarande i samma mapp, redigerar du *graphConfig. js* -filen för att `graphMeEndpoint` ange `graphMailEndpoint` och för `apiConfig` objektet.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Enter_the_Graph_Endpoint_Here>är slut punkten som API-anrop görs mot. * \<* För huvud-eller global Microsoft Graph API-tjänsten anger `https://graph.microsoft.com`du. Mer information finns i den [nationella moln distributionen](https://docs.microsoft.com/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>Steg 4: kör projektet

Kör projektet med en webb server med hjälp av [Node. js](https://nodejs.org/en/download/):

1. Starta-servern genom att köra följande kommandon inifrån projekt katalogen:
    ```bash
    npm install
    npm start
    ```
1. Bläddra till `http://localhost:3000/`.

1. Välj **Logga** in för att starta inloggnings processen och anropa sedan Microsoft Graph-API.

    Första gången du loggar in uppmanas du att ange ditt medgivande så att programmet kan komma åt din profil och logga in dig. När du har loggat in visas din användar profil information på sidan.

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Så här fungerar JavaScript-exempel-SPA: 1. SPA påbörjar inloggningen. 2. SPA hämtar en ID-token från Microsoft Identity Platform. 3. SPA-anropen hämtar token. 4. Microsoft Identity Platform returnerar en åtkomsttoken till SPA. 5. SPA gör-och HTTP GET-begäran med åtkomsttoken till Microsoft Graph-API: et. 6. Graph API returnerar ett HTTP-svar till SPA.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal. js

MSAL. js-biblioteket loggar in användare och begär de token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Exempel filen *index. html* innehåller en referens till biblioteket:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Du kan ersätta den tidigare versionen med den senaste utgivna versionen under [MSAL. js-versioner](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Om du har Node. js installerat kan du ladda ned den senaste versionen med hjälp av Package Manager för Node. js (NPM):

```batch
npm install msal
```

## <a name="next-steps"></a>Nästa steg

[MSAL. js GitHub-lagrings platsen](https://github.com/AzureAD/microsoft-authentication-library-for-js) innehåller ytterligare biblioteks dokumentation, vanliga frågor och svar och ger support.

En mer detaljerad steg-för-steg-guide om hur du skapar programmet för den här snabb starten finns i:

> [!div class="nextstepaction"]
> [Självstudie för att logga in och anropa MS Graph](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
