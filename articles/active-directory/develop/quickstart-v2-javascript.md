---
title: Logga in användare i JavaScript ensidiga appar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur en JavaScript-app kan anropa ett API som kräver åtkomsttoken med hjälp av Microsofts identitetsplattform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.openlocfilehash: ac134dce5ad739d1d81ef0c62a6bfb04468cafff
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991117"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Snabbstart: Logga in användare och få en åtkomsttoken i ett JavaScript SPA

I den här snabbstarten använder du ett kodexempel för att lära dig hur ett JavaScript-ensidigt program (SPA) kan logga in användare av personliga konton, arbetskonton och skolkonton. Ett JavaScript SPA kan också få en åtkomsttoken för att anropa Microsoft Graph API eller något webb-API. (Se [Hur exemplet fungerar](#how-the-sample-works) för en illustration.)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio-kod](https://code.visualstudio.com/download) (för att redigera projektfiler)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrera och ladda ned snabbstartsprogrammet
> Använd något av följande alternativ för att starta snabbstartsprogrammet.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1 (Express): Registrera och konfigurera appen automatiskt och hämta sedan kodexemplet
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig åtkomst till mer än en klient väljer du kontot längst upp till höger och ställer sedan in portalsessionen på den Azure Active Directory-klient (Azure AD) som du vill använda.
> 1. Gå till den nya [Azure-portalen - fönstret Appregistreringar.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)
> 1. Ange ett namn för ditt program. 
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Registrera**.
> 1. Följ instruktionerna för att hämta och konfigurera ditt nya program automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2 (Manuell): Registrera och konfigurera ditt program- och kodexempel manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
>
> 1. Om ditt konto ger dig åtkomst till mer än en klient väljer du ditt konto längst upp till höger och ställer sedan in portalsessionen på den Azure AD-klient som du vill använda.
> 1. Gå till sidan Microsoft identity platform for developers [App registrations.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Välj **Ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
> 1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
> 1. Välj **Registrera**. På sidan Översikt **över** appen noterar du **värdet program (klient)** för senare användning.
> 1. Den här snabbstarten kräver att [flödet för implicit beviljande](v2-oauth2-implicit-grant-flow.md) aktiveras. Välj **Autentisering**i den vänstra rutan i det registrerade programmet .
> 1. Under **Plattformskonfigurationer**väljer du **Lägg till en plattform**. En panel öppnas till vänster. Där väljer du området **Webbprogram.**
> 1. Fortfarande till vänster ställer du in `http://localhost:3000/` **redirect URI-värdet** på . Välj sedan **Åtkomsttoken** och **ID-token**.
> 1. Välj **Konfigurera**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> Om du vill att kodexemplet i `redirectUri` den `http://localhost:3000/` här snabbstarten ska fungera måste du lägga till en som och aktivera **Implicit bidrag**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-project"></a>Steg 2: Ladda ned projektet

> [!div renderon="docs"]
> Om du vill köra projektet med en webbserver med nod.js [hämtar du kärnprojektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal"]
> Köra projektet med en webbserver med nod.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Steg 3: Konfigurera JavaScript-appen
>
> Redigera *authConfig.js*i *JavaScriptSPA-mappen* och `clientID` `authority` ange `redirectUri` värdena och värdena under `msalConfig`.
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

> [!div renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Där:
> - Enter_the_Application_Id_Here>är **programmets (klient)-ID** för det program du registrerade. * \<*
> - Enter_the_Cloud_Instance_Id_Here>är instansen av Azure-molnet. * \<* För huvud- eller globala Azure-molnet anger du *https://login.microsoftonline.com*bara . För **nationella** moln (till exempel Kina), se [Nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - Enter_the_Tenant_info_here>är inställt på något av följande alternativ: * \<*
>    - Om ditt program stöder *konton i den här organisationskatalogen*ersätter du det här värdet med **klient-ID: n** eller **klientnamnet** (till exempel *contoso.microsoft.com*).
>    - Om programmet stöder *konton i en organisationskatalog* **ersätter**du det här värdet med organisationer .
>    - Om programmet stöder *konton i en organisationskatalog och personliga Microsoft-konton*ersätter du det här värdet med **vanliga**. Om du bara vill begränsa supporten till *personliga Microsoft-konton*ersätter du det här värdet med **konsumenter**.
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: Appen är konfigurerad och redo att köras
> Vi har konfigurerat ditt projekt med värden för appens egenskaper. 

> [!div renderon="docs"]
> 
> Redigera sedan *filen graphConfig.js* i samma mapp `graphMeEndpoint` för `graphMeEndpoint` att `apiConfig` ställa in och för objektet.
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
> Där:
> - Enter_the_Graph_Endpoint_Here>är slutpunkten som API-anrop kommer att göras mot. * \<* För den huvudsakliga eller globala Microsoft `https://graph.microsoft.com`Graph API-tjänsten anger du bara . Mer information finns i [Nationell molndistribution](https://docs.microsoft.com/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>Steg 4: Kör projektet

Kör projektet med en webbserver med [nod.js:](https://nodejs.org/en/download/)

1. Starta servern genom att köra följande kommando från projektkatalogen:
    ```batch
    npm install
    npm start
    ```
1. Öppna en webbläsare och `http://localhost:3000/`gå till .

1. Välj **Logga in** för att starta inloggningen och anropa sedan Microsoft Graph API.

När webbläsaren har läser in programmet väljer du **Logga in**. Första gången du loggar in uppmanas du att ge ditt samtycke så att programmet kan komma åt din profil och logga in dig. När du har loggat in ska din användarprofilinformation visas på sidan.

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Så här fungerar exempelappen i den här snabbstarten](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL-biblioteket loggar in användare och begär token som används för att komma åt ett API som skyddas av Microsofts identitetsplattform. Filen quickstart *index.html* innehåller en referens till biblioteket:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Du kan ersätta den föregående versionen med den senast utgivna versionen under [MSAL.js-versioner](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Alternativt, om du har Node.js installerat, kan du ladda ner den senaste versionen via Node.js Package Manager (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL-initiering

Snabbstartskoden visar också hur du initierar MSAL-biblioteket:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };  

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Var  |  |
> |---------|---------|
> |`clientId`     | Program-ID för programmet som är registrerat i Azure-portalen.|
> |`authority`    | (Valfritt) Den myndighets-URL som stöder kontotyper, som tidigare beskrivits i konfigurationsavsnittet. Standardutfärdaren `https://login.microsoftonline.com/common`är . |
> |`redirectUri`     | Programregistreringens konfigurerade svar/redirectUri. I det `http://localhost:3000/`här fallet . . |
> |`cacheLocation`  | (Valfritt) Anger webbläsarlagring för auth-tillståndet. Standard är sessionStorage.   |
> |`storeAuthStateInCookie`  | (Valfritt) Biblioteket som lagrar tillståndet för autentiseringsbegäran som krävs för validering av autentiseringsflödena i webbläsarcookiesna. Denna cookie är inställd för IE och Edge webbläsare för att mildra vissa [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Mer information om tillgängliga konfigurerbara alternativ finns i [Initiera klientprogram](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Logga in användare

Följande kodavsnitt visar hur du loggar in användare:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Var  |  |
> |---------|---------|
> | `scopes`   | (Valfritt) Innehåller scope som begärs för användarens medgivande vid inloggning. Till exempel `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`). |

> [!TIP]
> Du kanske vill använda `loginRedirect` metoden för att omdirigera den aktuella sidan till inloggningssidan i stället för ett popup-fönster.

### <a name="request-tokens"></a>Begära token

MSAL använder tre metoder för `acquireTokenRedirect` `acquireTokenPopup`att hämta token: , och`acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar hämtning och förnyelse av token utan någon användarinteraktion. När metoden `loginRedirect` eller `loginPopup` har körts för första gången är `acquireTokenSilent` den metod som vanligtvis används för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop. Anrop för att begära eller förnya token görs tyst.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Var  |  |
> |---------|---------|
> | `scopes`   | Innehåller omfång som begärs att returneras i åtkomsttoken för API. Till exempel `[ "mail.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API:er (det vill säga `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Det finns situationer där du måste tvinga användare att interagera med slutpunkten för Microsoft-identitetsplattformen. Ett exempel:
* Användare kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla.
* Ditt program begär åtkomst till ytterligare resursomfattningar som användaren behöver samtycka till.
* Tvåfaktorsautentisering krävs.

Det vanliga rekommenderade mönstret för `acquireTokenSilent` de flesta program är att `acquireTokenPopup` ringa `acquireTokenRedirect`först, sedan fånga undantaget och sedan ringa (eller) för att starta en interaktiv begäran.

Anropar `acquireTokenPopup` resultatet i ett popup-fönster för att logga in. (Eller `acquireTokenRedirect` resulterar i att omdirigera användare till slutpunkten för Microsoft identity platform.) I det fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge medgivande till den nödvändiga resursen eller slutföra tvåfaktorsautentiseringen.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Den här snabbstarten använder metoderna `loginRedirect` och `acquireTokenRedirect` med Microsoft Internet Explorer på grund av ett känt [problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som rör hantering av popup-fönster av Internet Explorer.

## <a name="next-steps"></a>Nästa steg

En mer detaljerad steg-för-steg-guide om hur du skapar programmet för den här snabbstarten finns i:

> [!div class="nextstepaction"]
> [Självstudiekurs för att logga in och anropa MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Information om hur du surfar på MSAL-osien för dokumentation, vanliga frågor, problem med mera finns i:

> [!div class="nextstepaction"]
> [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)
