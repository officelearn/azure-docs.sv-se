---
title: Microsoft Identity Platform JavaScript-snabb start – Azure
description: Lär dig hur JavaScript-program kan anropa ett API som kräver åtkomsttoken med hjälp av Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59b5ddbff646104b3d4a35c26c1ecf3968dea31d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852911"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Snabbstart: Logga in användare och hämta en åtkomsttoken från ett program med en enda sida med Java Script

I den här snabb starten får du lära dig hur du använder ett kod exempel som visar hur ett Java Script-program (Single-Side Application) kan logga in användare av personliga konton, arbets konton och skol konton. En JavaScript-SPA kan också hämta en åtkomsttoken för att anropa Microsoft Graph-API: et eller något webb-API.

![Hur exempel programmet i den här snabb starten fungerar](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten kräver följande konfiguration:
* Om du vill köra projektet med en Node. js-Server laddar du ned och installerar [Node. js](https://nodejs.org/en/download/).
* Om du vill redigera projektfiler laddar du ned och installerar [Visual Studio Code](https://code.visualstudio.com/download).
* Om du vill köra projektet som en Visual Studio-lösning kan du ladda ned och installera [Visual studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Starta snabb starts programmet med något av följande alternativ.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1 (Express): Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
> 1. Om ditt konto ger dig åtkomst till fler än en klient väljer du kontot längst upp till höger och anger sedan din portal-session till den Azure Active Directory (Azure AD) som du vill använda.
> 1. Gå till fönstret ny [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2 (manuell): Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
>
> 1. Om ditt konto ger dig åtkomst till fler än en klient väljer du ditt konto längst upp till höger och anger sedan din portal-session till den Azure AD-klient som du vill använda.
> 1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Under avsnittet omdirigerings- **URI** väljer du **webb** plattformen i list rutan och anger värdet till `http://localhost:30662/`.
> 1. Välj **Registrera**. På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
> 1. Den här snabbstarten kräver att [flödet för implicit beviljande](v2-oauth2-implicit-grant-flow.md) aktiveras. I det vänstra fönstret i det registrerade programmet väljer du **autentisering**.
> 1. I avsnittet **Avancerade inställningar** , under **implicit tilldelning**, markerar du kryss rutorna **ID-token** och **åtkomst** -token. ID-token och åtkomsttoken krävs, eftersom den här appen måste logga in användare och anropa ett API.
> 1. Klicka på **Spara**längst upp i fönstret.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera ditt program i Azure Portal
> För att kod exemplet för den här snabb starten ska fungera måste du lägga till en omdirigerings-URI som `http://localhost:30662/` och aktivera **implicit beviljande**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

Välj det alternativ som passar din utvecklings miljö:

* Om du vill köra projektet med en webb server med hjälp av Node. js [laddar du ned de centrala projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Öppna filerna med ett redigerings program, till exempel [Visual Studio Code](https://code.visualstudio.com/).

* Valfritt Om du vill köra projektet med IIS-servern [laddar du ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Extrahera zip-filen till en lokal mapp (till exempel *C:\Azure-samples*).



#### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera din JavaScript-app

> [!div renderon="docs"]
> I mappen *JavaScriptSPA* redigerar du *index. html*och anger värdena och `clientID` `authority` för under `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> I mappen *JavaScriptSPA* redigerar du *index. html*och ersätter `msalConfig` med följande kod:

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
> [!div renderon="portal"]
> > [!NOTE]
> > Den här snabb starten stöder Enter_the_Supported_Account_Info_Here.


> [!div renderon="docs"]
>
> Där:
> - Enter_the_Application_Id_here > är **program-ID: t (Client)** för det program som du har registrerat.  *\<*
> - Enter_the_Tenant_info_here > har angetts till något av följande alternativ:  *\<*
>    - Om ditt program har stöd *för konton i den här organisations katalogen*ersätter du värdet med **klient-ID** eller **klient namn** (till exempel *contoso.Microsoft.com*).
>    - Om ditt program har stöd *för konton i en organisations katalog*ersätter du värdet med **organisationer**.
>    - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton*ersätter du värdet med **vanligt**. Om du bara vill begränsa stödet till *personliga Microsoft-konton*ersätter du värdet med **konsumenter**.
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)** , **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.
>

#### <a name="step-4-run-the-project"></a>Steg 4: Kör projektet

* Om du använder [Node. js](https://nodejs.org/en/download/):

    1. Starta-servern genom att köra följande kommando från projekt katalogen:

        ```batch
        npm install
        node server.js
        ```

    1. Öppna en webbläsare och gå till `http://localhost:30662/`.
    1. Välj **Logga** in för att starta inloggningen och anropa Microsoft Graph API.


* Om du använder [Visual Studio](https://visualstudio.microsoft.com/downloads/)väljer du projekt lösningen och väljer sedan F5 för att köra projektet.

När webbläsaren har läst in programmet väljer du **Logga**in. Första gången du loggar in uppmanas du att ange ditt medgivande för att ge programmet åtkomst till din profil och att logga in dig. När du har loggat in visas din användar profil information på sidan.

## <a name="more-information"></a>Mer information

### <a name="msaljs"></a>msal.js

MSAL-biblioteket loggar in användare och begär de token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Filen starter *index. html* innehåller en referens till biblioteket:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Du kan ersätta den tidigare versionen med den senaste utgivna versionen under [MSAL. js-versioner](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Om du har Node. js installerat kan du ladda ned den senaste versionen via Node. js-paket hanteraren (NPM):

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL-initiering

Snabb starts koden visar också hur du initierar MSAL-biblioteket:

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

> |Där  |  |
> |---------|---------|
> |`ClientId`     | Program-ID för programmet som är registrerat i Azure Portal.|
> |`authority`    | Valfritt Auktoritets-URL: en som stöder konto typer, enligt beskrivningen ovan i avsnittet konfiguration. Standard auktoriteten är `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | Valfritt Anger webb läsar lagring för auth-tillstånd. Standardvärdet är sessionStorage.   |
> |`storeAuthStateInCookie`  | Valfritt Biblioteket som lagrar det tillstånd för autentiseringsbegäran som krävs för verifiering av autentiserings flöden i webbläsarens cookies. Den här cookien är inställd för IE-och Edge-webbläsare för att minimera vissa [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Mer information om tillgängliga konfigurerbara alternativ finns i [initiera klient program](msal-js-initializing-client-applications.md).

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

> |Där  |  |
> |---------|---------|
> | `scopes`   | Valfritt Innehåller omfattningar som begärs för användar medgivande vid inloggnings tillfället. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`). |

> [!TIP]
> Du kanske vill använda `loginRedirect` metoden för att omdirigera den aktuella sidan till inloggnings sidan i stället för ett popup-fönster.

### <a name="request-tokens"></a>Begära token

MSAL använder tre metoder för att hämta tokens: `acquireTokenRedirect`, `acquireTokenPopup`, och`acquireTokenSilent`

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

> |Där  |  |
> |---------|---------|
> | `scopes`   | Innehåller omfång som begärs att returneras i åtkomsttoken för API. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Det finns situationer där du måste tvinga användare att interagera med Microsoft Identity Platform-slutpunkten. Exempel:
* Användare kan behöva ange sina autentiseringsuppgifter på grund av att deras lösen ord har upphört att gälla.
* Ditt program begär åtkomst till ytterligare resurs omfattningar som användaren behöver godkänna.
* Tvåfaktorautentisering krävs.

Det vanligaste rekommenderade mönstret för de flesta program är att `acquireTokenSilent` anropa först, sedan fånga undantagen och sedan anropa `acquireTokenPopup` (eller `acquireTokenRedirect`) för att starta en interaktiv begäran.

Anropar `acquireTokenPopup` resultaten i ett popup-fönster för att logga in. (Eller `acquireTokenRedirect` leder till omdirigering av användare till Microsoft Identity Platform-slutpunkten.) I det fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge det tillstånd till den begärda resursen eller utföra tvåfaktorautentisering.

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
> I den här snabb `loginRedirect` starten `acquireTokenRedirect` används metoderna och med Microsoft Internet Explorer, på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som rör hanteringen av popup-fönster i Internet Explorer.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad steg-för-steg-guide om hur du skapar programmet för den här snabb starten finns i:

> [!div class="nextstepaction"]
> [Självstudie för att logga in och anropa MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Om du vill bläddra bland MSAL-lagrings platsen för dokumentation, vanliga frågor och svar, se:

> [!div class="nextstepaction"]
> [MSAL. js GitHub-lagrings platsen](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)