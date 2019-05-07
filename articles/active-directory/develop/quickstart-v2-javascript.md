---
title: Microsoft identity-plattformen JavaScript Snabbstart | Azure
description: Lär dig hur JavaScript-program kan anropa ett API som kräver åtkomsttoken genom Microsoft identity-plattformen.
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
ms.openlocfilehash: 605206682cb70d430773cdbf9ff746eabf594103
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190848"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application-spa"></a>Snabbstart: Logga in användare och hämta en åtkomsttoken från ett JavaScript ensidesapplikation (SPA)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

I den här snabbstarten får du lära dig hur du använder ett kodexempel som visar hur en JavaScript ensidesapplikation (SPA) kan logga in personliga konton, fungerar och skolkonton och få en åtkomsttoken att anropa Microsoft Graph API eller alla webb-API.

![Visar hur exempelapp som genererats av den här snabbstarten fungerar](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande inställningar för den här snabbstarten:
* Kör projektet med en node.js-server
    * Installera [Node.js](https://nodejs.org/en/download/)
    * Installera [Visual Studio Code](https://code.visualstudio.com/download) redigera projektfiler
* Om du vill köra projektet som en Visual Studio-lösning, installera [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Gå till den nya [Azure portal – appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) fönstret.
> 1. Ange ett namn för programmet och klicka på **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan.
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Webb**-plattformen i avsnittet **Omdirigerings-URI** och ange värdet till `http://localhost:30662/`.
> 1. När det är klart väljer du **Registrera**.  På appens sida **Översikt** antecknar du värdet för **Application (client) ID** (Program-ID (klient)).
> 1. Den här snabbstarten kräver att [flödet för implicit beviljande](v2-oauth2-implicit-grant-flow.md) aktiveras. I det vänstra navigeringsfönstret för det registrerade programmet väljer du **Autentisering**.
> 1. I **Avancerade inställningar** går du till **Implicit beviljande** och aktiverar kryssrutorna **ID-token** och **Åtkomsttoken**. ID-token och åtkomsttoken krävs eftersom den här appen behöver logga in användare och anropa ett API.
> 1. Välj **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera ditt program i Azure Portal
> För att kodexemplet för den här snabbstarten ska fungera behöver du lägga till en omdirigerings-URI som `http://localhost:30662/` och aktivera **Implicit beviljande**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

Du kan välja något av följande alternativ som är lämpliga för utvecklingsmiljön.
* [Hämta projektfilerna core](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip), för att köra med en webbserver med Node.js. Öppna filerna genom att använda en redigerare som [Visual Studio Code](https://code.visualstudio.com/).

* (Valfritt) [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip), för att köra med IIS-servern. Extrahera zip-filen i en lokal mapp, till exempel **C:\Azure-Samples**.



#### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera din JavaScript-app

> [!div renderon="docs"]
> Under mappen *JavaScriptSPA*, redigera `index.html` och ange den `clientID` och `authority` värden under `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Under mappen *JavaScriptSPA*, redigera `index.html` och Ersätt `msalConfig` med:

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
> [!div renderon="docs"]
>
> Där:
> - `Enter_the_Application_Id_here` – är **program-ID (klient)** för programmet som du har registrerat.
> - `Enter_the_Tenant_Info_Here` – är inställt på något av följande alternativ:
>   - Om ditt program stöder **Endast konton i den här organisationskatalogen** ska du ersätta värdet med **klient-ID** eller **klientnamn** (till exempel contoso.microsoft.com)
>   - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
>   - Om ditt program stöder **konton alla organisationskatalog och personliga Microsoft-konton**, Ersätt detta värde med `common`. Att begränsa stödet till *personliga Microsoft-konton endast*, Ersätt detta värde med `consumers`.
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.
>

#### <a name="step-4-run-the-project"></a>Steg 4: Kör projektet

* Om du använder [Node.js](https://nodejs.org/en/download/):

    1. Kör följande kommando från projektkatalogen för att starta servern:

        ```batch
        npm install
        node server.js
        ```

    1. Öppna en webbläsare och gå till `http://localhost:30662/`.
    1. Klicka på knappen **Logga In** för att börja logga in och anropa sedan Microsoft Graph API.


* Om du använder [Visual Studio](https://visualstudio.microsoft.com/downloads/), se till att markera projektet lösningen och tryck sedan på **F5** köra projektet.

När webbläsaren läser in programmet, klickar du på **logga In**.  Första gången du loggar in uppmanas du att ge ditt medgivande att ge programmet åtkomst till din profil och logga in dig på. Du bör se din användarprofilsinformation som visas på sidan på lyckades logga in.

## <a name="more-information"></a>Mer information

### <a name="msaljs"></a>*msal.js*

MSAL finns i biblioteket som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft identity-plattformen. Snabbstartens *index.html* innehåller en referens till biblioteket:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Du kan ersätta senare version med den senaste utgivna versionen under [MSAL.js släpper](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Om du har noden installerad kan kan du hämta den senaste versionen via npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL-initiering

Snabbstartskoden visar även hur du initierar biblioteket:

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
> |`ClientId`     |Program-ID från appen som registrerats i Azure-portalen|
> |`authority`    | (Valfritt) Det är utfärdarens URL enligt beskrivningen i konfigurationsavsnittet ovan för kontotyper. Standard-utfärdare är `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Valfritt) Detta anger webbläsare lagringen för auth-tillstånd. Standardvärdet är sessionStorage.   |
> |`storeAuthStateInCookie`  | (Valfritt) Biblioteket lagrar tillståndet autentisering som krävs för verifiering av auth-flöden i cookies. Detta är inställt för IE och Edge-webbläsare att åtgärda vissa [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

 Se den [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) för mer information om de konfigurerbara alternativ som är tillgängliga.

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
> | `scopes`   | (Valfritt) Innehåller omfång som begärs för användarmedgivande vid tiden för inloggning. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`). |

> [!TIP]
> Alternativt kan du använda metoden `loginRedirect` för att omdirigera den aktuella sidan till inloggningssidan i stället för ett popup-fönster.

### <a name="request-tokens"></a>Begära token

MSAL har tre metoder som används för att hämta token: `acquireTokenRedirect`, `acquireTokenPopup` och `acquireTokenSilent`

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

Det finns situationer då du behöver att tvinga användare att interagera med Microsoft identity-plattformen slutpunkt. Exempel:
* Användarna kan behöva ange sina autentiseringsuppgifter igen eftersom deras lösenord har upphört att gälla
* Ditt program begär åtkomst till en ytterligare resursomfång som användaren behöver ge sitt medgivande för
* Tvåfaktorsautentisering krävs

Det vanligtvis rekommenderade mönstret för de flesta program är att först anropa `acquireTokenSilent`, sedan fånga upp undantaget och sedan anropa `acquireTokenPopup` (eller `acquireTokenRedirect`) för att starta en interaktiv begäran.

Anropa den `acquireTokenPopup` resulterar i ett popup-fönster för att logga in (eller `acquireTokenRedirect` resulterar i att omdirigera användare till Microsoft identity-plattformen slutpunkten) där användare behöver interagera genom att kontrollera sina autentiseringsuppgifter kan ge samtycke till de nödvändiga resurs eller du har slutfört tvåfaktorautentisering.

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
> Den här snabbstarten används de `loginRedirect` och `acquireTokenRedirect` metoder när webbläsaren som används är Internet Explorer på grund av en [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) rör hantering av popup-fönster genom att webbläsaren Internet Explorer.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad stegvis guide om hur du skapar programmet för den här snabbstarten finns i JavaScript-självstudien nedan.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Lär dig stegen för att skapa programmet för den här snabbstarten

> [!div class="nextstepaction"]
> [Självstudie för att logga in och anropa MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>På MSAL-lagringsplatsen hittar du dokumentation, vanliga frågor och svar, kända problem och annat

> [!div class="nextstepaction"]
> [MSAL.js GitHub-lagringsplatsen](https://github.com/AzureAD/microsoft-authentication-library-for-js)
