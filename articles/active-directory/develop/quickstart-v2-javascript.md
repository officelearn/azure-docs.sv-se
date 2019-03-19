---
title: Snabbstart om Azure AD v2 JavaScript | Microsoft Docs
description: Lär dig hur JavaScript-program kan anropa ett API som kräver åtkomsttoken genom Azure Active Directory v2.0-slutpunkten
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 694eb49244afb6761ce274572f15273ccef6cbb5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195038"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Snabbstart: Logga in användare och hämta en åtkomsttoken från ett JavaScript-program

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

I den här snabbstarten lär du dig att använda ett kodexempel som visar hur en JavaScript-ensidesapplikation (SPA) kan logga in personliga konton och arbets- och skolkonton samt hämta en åtkomsttoken för att anropa Microsoft Graph API eller valfritt webb-API.

![Så här fungerar den exempelapp som skapas med den här snabbstarten](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure-portalen – Programregistrering (förhandsversion)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Ange ett namn för ditt program och klicka på **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com/) för att registrera ett program.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion) > Ny registrering**.
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
* [Ladda ned viktiga projektfiler – för en webbserver, till exempel Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Extrahera zip-filen i en lokal mapp, till exempel **C:\Azure-Samples**.
Öppna filerna i mappen genom att använda en redigerare som [Visual Studio Code](https://code.visualstudio.com/).

#### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera din JavaScript-app

> [!div renderon="docs"]
> Under mappen *JavaScriptSPA*, redigera `index.html` och ange den `clientID` och `authority` värden under `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Under mappen *JavaScriptSPA*, redigera `index.html` och Ersätt `applicationConfig` med:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> Där:
> - `Enter_the_Application_Id_here` – är **program-ID (klient)** för programmet som du har registrerat.
> - `Enter_the_Tenant_Info_Here` – är inställt på något av följande alternativ:
>   - Om ditt program stöder **Endast konton i den här organisationskatalogen** ska du ersätta värdet med **klient-ID** eller **klientnamn** (till exempel contoso.microsoft.com)
>   - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
>   - Om ditt program stöder **Konton i en valfri organisationskatalog och personliga Microsoft-konton** ersätter du värdet med `common`
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.

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

## <a name="more-information"></a>Mer information

### <a name="msaljs"></a>*msal.js*

MSAL är det bibliotek som används för att logga in användare och begära token som används för åtkomst till ett API som skyddas av Microsoft Azure Active Directory (Azure AD). Snabbstartens *index.html* innehåller en referens till biblioteket:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Alternativet kan du ladda ned det via npm om du har Node installerat:

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL-initiering

Snabbstartskoden visar även hur du initierar biblioteket:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Var  |  |
> |---------|---------|
> |`ClientId`     |Program-ID från appen som registrerats i Azure-portalen|
> |`authority`    |Det är utfärdar-URL:en. När *null*-uppsättningar skickas anges standardbehörigheten till `https://login.microsoftonline.com/common`. Om din app är till för en enskild klientorganisation (med konton i endast en katalog som mål) anger du det här värde till `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Motringningsmetoden anropas när autentiseringen har omdirigerat tillbaka till appen. Här skickas `acquireTokenRedirectCallBack`. Det här är null om loginPopup används.|
> |`options`  |En samling med valfria parametrar. I det här fallet är `storeAuthStateInCookie` och `cacheLocation` valfri konfiguration. Se [wikin](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) för mer information om alternativen. |

### <a name="sign-in-users"></a>Logga in användare

Följande kodavsnitt visar hur du loggar in användare:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Var  |  |
> |---------|---------|
> | `scopes`   | (Valfritt) Innehåller omfång som begärs för användarmedgivande vid tiden för inloggning. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`). Här skickas `applicationConfig.graphScopes`. |

> [!TIP]
> Alternativt kan du använda metoden `loginRedirect` för att omdirigera den aktuella sidan till inloggningssidan i stället för ett popup-fönster.

### <a name="request-tokens"></a>Begära token

MSAL har tre metoder som används för att hämta token: `acquireTokenRedirect`, `acquireTokenPopup` och `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar hämtning och förnyelse av token utan någon användarinteraktion. När metoden `loginRedirect` eller `loginPopup` har körts för första gången är `acquireTokenSilent` den metod som vanligtvis används för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop. Anrop för att begära eller förnya token görs tyst.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Var  |  |
> |---------|---------|
> | `scopes`   | Innehåller omfång som begärs att returneras i åtkomsttoken för API. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`). Här skickas `applicationConfig.graphScopes`.|

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Det finns situationer då du behöver att tvinga användare att interagera med Azure AD v2.0-slutpunkten. Exempel:
* Användarna kan behöva ange sina autentiseringsuppgifter igen eftersom deras lösenord har upphört att gälla
* Ditt program begär åtkomst till en ytterligare resursomfång som användaren behöver ge sitt medgivande för
* Tvåfaktorsautentisering krävs

Det vanligtvis rekommenderade mönstret för de flesta program är att först anropa `acquireTokenSilent`, sedan fånga upp undantaget och sedan anropa `acquireTokenRedirect` (eller `acquireTokenPopup`) för att starta en interaktiv begäran.

När `acquireTokenPopup(scope)` anropas visas ett popup-fönster för inloggning (eller `acquireTokenRedirect(scope)`, som gör att användarna omdirigeras till Azure AD v2.0-slutpunkten), där användare måste interagera genom att antingen bekräfta sina autentiseringsuppgifter och ge medgivande till den begärda resursen, eller slutföra tvåfaktorsautentiseringen.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> I den här snabbstarten används metoderna `loginRedirect` och `acquireTokenRedirect` när den webbläsare som används är Internet Explorer på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som rör hantering av popup-fönster i webbläsaren Internet Explorer.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad stegvis guide om hur du skapar programmet för den här snabbstarten finns i JavaScript-självstudien nedan.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Lär dig stegen för att skapa programmet för den här snabbstarten

> [!div class="nextstepaction"]
> [Självstudie för att logga in och anropa MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>På MSAL-lagringsplatsen hittar du dokumentation, vanliga frågor och svar, kända problem och annat

> [!div class="nextstepaction"]
> [MSAL.js GitHub-lagringsplatsen](https://github.com/AzureAD/microsoft-authentication-library-for-js)
