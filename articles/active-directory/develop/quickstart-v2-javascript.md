---
title: Microsoft identity-plattformen JavaScript-Snabbstart – Azure
description: Lär dig hur JavaScript-program kan anropa ett API som kräver åtkomsttoken med hjälp av Microsoft identity-plattformen.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 305479c8872883e434731b5062b408f97f68cc43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055934"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Snabbstart: Logga in användare och hämta en åtkomsttoken från ett enkelsidigt program för JavaScript

I den här snabbstarten får du lära dig hur du använder ett kodexempel som visar hur en JavaScript ensidesapplikation (SPA) kan logga in användare av personliga konton och arbetskonton skolkonton. Ett JavaScript SPA kan också hämta en åtkomsttoken att anropa Microsoft Graph API eller alla webb-API.

![Så här exempelappen i den här snabbstarten fungerar](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här snabbstarten kräver följande inställningar:
* Kör projektet med en Node.js-server genom att ladda ned och installera [Node.js](https://nodejs.org/en/download/).
* Om du vill redigera projektfiler, ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/download).
* Kör projektet som en Visual Studio-lösning genom att ladda ned och installera [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Starta programmet Snabbstart med något av följande alternativ.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1 (Express): Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Logga in på den [Azure-portalen](https://portal.azure.com) genom att använda ett arbets eller skolkonto eller ett personligt microsoftkonto.
> 1. Om ditt konto ger dig tillgång till fler än en klient väljer du kontot längst upp till höger och sedan ange din portal session till Azure Active Directory (Azure AD)-klient som du vill använda.
> 1. Gå till den nya [Azure portal – appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) fönstret.
> 1. Ange ett namn för ditt program och välj **registrera**.
> 1. Följ anvisningarna för att hämta och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2 (manuellt): Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på den [Azure-portalen](https://portal.azure.com) genom att använda ett arbets eller skolkonto eller ett personligt microsoftkonto.
>
> 1. Om ditt konto ger dig tillgång till fler än en klient väljer du ditt konto längst upp till höger och sedan ange din portal session till Azure AD-klient som du vill använda.
> 1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan.
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Under den **omdirigerings-URI** , i listrutan, väljer du den **Web** plattform, och sedan ange värdet till `http://localhost:30662/`.
> 1. Välj **Registrera**. I appen **översikt** , Lägg den **(klient)-ID: T** värdet för senare användning.
> 1. Den här snabbstarten kräver att [flödet för implicit beviljande](v2-oauth2-implicit-grant-flow.md) aktiveras. I den vänstra rutan i det registrerade programmet, väljer **autentisering**.
> 1. I den **avancerade inställningar** under avsnittet **Implicit beviljande**väljer den **ID-token** och **åtkomsttoken** kryssrutorna. ID-token och åtkomsttoken krävs, eftersom den här appen behöver logga in användare och anropa ett API.
> 1. Längst ned i fönstret Välj **spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera ditt program i Azure Portal
> För kodexempel för den här snabbstarten ska fungera måste du Lägg till en omdirigerings-URI som `http://localhost:30662/` och aktivera **Implicit beviljande**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

Välj det alternativ som passar din utvecklingsmiljö:

* Kör projektet med en webbserver med Node.js, [hämta projektfilerna core](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Öppna filerna genom att använda en redigerare som [Visual Studio Code](https://code.visualstudio.com/).

* (Valfritt) Kör projektet med IIS-server [ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Extrahera zip-filen till en lokal mapp (till exempel *C:\Azure-Samples*).



#### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera din JavaScript-app

> [!div renderon="docs"]
> I den *JavaScriptSPA* mappen redigera *index.html*, och ange den `clientID` och `authority` värden under `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> I den *JavaScriptSPA* mappen redigera *index.html*, och Ersätt `msalConfig` med följande kod:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="docs"]
>
> Där:
> - *\<Enter_the_Application_Id_here >* är den **(klient)-ID: T** för programmet som du har registrerat.
> - *\<Enter_the_Tenant_info_here >* är inställd på något av följande alternativ:
>    - Om ditt program stöder *konton i den här organisationskatalog*, Ersätt detta värde med den **klient-ID** eller **klientnamn** (till exempel  *Contoso.microsoft.com*).
>    - Om ditt program stöder *konton i alla organisationskatalog*, Ersätt detta värde med **organisationer**.
>    - Om ditt program stöder *konton alla organisationskatalog och personliga Microsoft-konton*, Ersätt detta värde med **vanliga**. Att begränsa stödet till *personliga Microsoft-konton endast*, Ersätt detta värde med **konsumenter**.
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)** , **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.
>

#### <a name="step-4-run-the-project"></a>Steg 4: Kör projektet

* Om du använder [Node.js](https://nodejs.org/en/download/):

    1. Kör följande kommando för att starta servern från projektkatalogen:

        ```batch
        npm install
        node server.js
        ```

    1. Öppna en webbläsare och gå till `http://localhost:30662/`.
    1. Välj **logga In** att starta inloggningen och sedan anropa Microsoft Graph API.


* Om du använder [Visual Studio](https://visualstudio.microsoft.com/downloads/), Välj projekt-lösning och därefter på F5 för att köra projektet.

När webbläsaren har lästs in programmet, Välj **logga In**. Första gången du loggar in uppmanas du att ge ditt medgivande att ge programmet åtkomst till din profil och logga in dig på. När du är inloggad har ska din användarprofilsinformation visas på sidan.

## <a name="more-information"></a>Mer information

### <a name="msaljs"></a>msal.js

MSAL biblioteket loggar in användare och begär de token som används för att få åtkomst till ett API som skyddas av Microsoft identity-plattformen. Snabbstarten *index.html* filen innehåller en referens till biblioteket:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Du kan ersätta den föregående versionen med den senaste utgivna versionen under [MSAL.js släpper](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Om du har installerat Node.js, kan du hämta den senaste versionen via Node.js Package Manager (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL-initiering

Snabbstartskoden visar också hur du initierar MSAL-biblioteket:

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

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Var  |  |
> |---------|---------|
> |`ClientId`     | Program-ID för det program som har registrerats i Azure-portalen.|
> |`authority`    | (Valfritt) URL för utfärdare som stöder kontotyper, enligt beskrivningen tidigare i konfigurationsavsnittet. Standard-utfärdare är `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Valfritt) Anger webbläsare lagringen för auth-tillstånd. Standardvärdet är sessionStorage.   |
> |`storeAuthStateInCookie`  | (Valfritt) I biblioteket som lagrar frågestatusen för autentisering som krävs för verifiering av autentiseringsflöden i cookies. Den här cookien som har angetts för IE och Edge-webbläsare att åtgärda vissa [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Mer information om tillgängliga konfigurerbara alternativ som finns i [initiera klientprogram](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Logga in användare

Följande kodavsnitt visar hur du loggar in användare:

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Var  |  |
> |---------|---------|
> | `scopes`   | (Valfritt) Innehåller omfattningar som begärs för användarens medgivande på inloggningstid. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`). |

> [!TIP]
> Du kanske också vill använda den `loginRedirect` metod för att omdirigera den aktuella sidan till inloggningssidan i stället för ett popup-fönster.

### <a name="request-tokens"></a>Begära token

MSAL använder tre metoder för att hämta token: `acquireTokenRedirect`, `acquireTokenPopup`, och `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar hämtning och förnyelse av token utan någon användarinteraktion. När metoden `loginRedirect` eller `loginPopup` har körts för första gången är `acquireTokenSilent` den metod som vanligtvis används för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop. Anrop för att begära eller förnya token görs tyst.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Var  |  |
> |---------|---------|
> | `scopes`   | Innehåller omfång som begärs att returneras i åtkomsttoken för API. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Det finns situationer då du behöver att tvinga användare att interagera med Microsoft identity-plattformen slutpunkten. Exempel:
* Användarna kan behöva ange sina autentiseringsuppgifter på nytt eftersom deras lösenord har upphört att gälla.
* Ditt program begär åtkomst till resurser omfattningar som användaren behöver för att godkänna.
* Tvåfaktorsautentisering krävs.

Det rekommendera vanliga mönstret för de flesta program är att anropa `acquireTokenSilent` först fånga upp undantaget och sedan anropa `acquireTokenPopup` (eller `acquireTokenRedirect`) att starta en interaktiv förfrågan.

Anropa den `acquireTokenPopup` resulterar i ett popup-fönster för att logga in. (Eller `acquireTokenRedirect` resulterar i att omdirigera användare till Microsoft identity-plattformen slutpunkten.) I fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge samtycke till den begärda resursen eller du har slutfört tvåfaktorsautentisering.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> [!NOTE]
> Den här snabbstarten används de `loginRedirect` och `acquireTokenRedirect` metoder med Microsoft Internet Explorer grund av en [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) rör hantering av popup-fönster i Internet Explorer.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad vägledning om hur du skapar program för den här snabbstarten finns här:

> [!div class="nextstepaction"]
> [Självstudie för att logga in och anropa MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Om du vill bläddra MSAL lagringsplatsen för dokumentation, se vanliga frågor och svar, problem och mer:

> [!div class="nextstepaction"]
> [MSAL.js GitHub-lagringsplatsen](https://github.com/AzureAD/microsoft-authentication-library-for-js)
