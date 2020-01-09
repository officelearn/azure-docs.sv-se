---
title: Själv studie kurs om JavaScript-program med enkel sida – Microsoft Identity Platform | Azure
description: Hur Java Script SPA-program kan anropa ett API som kräver åtkomsttoken från Azure Active Directory v 2.0-slutpunkt
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 995210e720040448927b2aaac2c2eb590e3de981
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423380"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Logga in användare och anropa Microsoft Graph-API: et från ett Java Script (Single-Side Application)

Den här guiden visar hur ett Java Script (Single-Side Application) kan:
- Logga in personliga konton, samt arbets-och skol konton 
- Hämta en åtkomsttoken
- Anropa API: et för Microsoft Graph eller andra API: er som kräver åtkomsttoken från *Microsoft Identity Platform-slutpunkten*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hur exempel appen som genereras av den här hand boken fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Det exempel program som skapats av den här guiden gör det möjligt för ett Java Script SPA att fråga Microsoft Graph-API eller ett webb-API som accepterar token från Microsoft Identity Platform-slutpunkten. I det här scenariot begärs en åtkomsttoken efter att användaren loggar in och läggs till i HTTP-begäranden via Authorization-huvudet. Hämtning av token och förnyelse hanteras av Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

I den här guiden används följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library för Java Script Preview|

> [!NOTE]
> *Msal. js* riktar sig till Microsoft Identity Platform-slutpunkten, som möjliggör personliga konton och skolan och arbets konton för att logga in och förvärva token. Slut punkten för Microsoft Identity Platform har [vissa begränsningar](azure-ad-endpoint-comparison.md#limitations).
> Information om skillnaderna mellan v 1.0 och v 2.0-slut punkter finns i [jämförelse guiden för slut punkten](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Konfigurera din webb server eller ditt projekt

> Vill du ladda ned det här exemplets projekt i stället? Gör något av följande:
> 
> - Om du vill köra projektet med hjälp av en lokal webb server, till exempel Node. js, [laddar du ned projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - Valfritt För att köra projektet med hjälp av Microsoft Internet Information Services (IIS)-servern, [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Om du vill konfigurera kod exemplet innan du kör det går du vidare till [konfigurations steget](#register-your-application).

## <a name="prerequisites"></a>Krav

* För att kunna köra den här självstudien behöver du en lokal webb server, till exempel [Node. js](https://nodejs.org/en/download/), [.net Core](https://www.microsoft.com/net/core)eller IIS Express-integrering med [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Om du använder Node. js för att köra projektet installerar du en Integrated Development Environment (IDE), till exempel [Visual Studio Code](https://code.visualstudio.com/download), för att redigera projektfilerna.

* Anvisningarna i den här guiden baseras på både Node. js och Visual Studio 2017, men du kan använda en annan utvecklings miljö eller webb server.

## <a name="create-your-project"></a>Skapa ditt projekt

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Alternativ 1: Node. js eller andra webb servrar
> Kontrol lera att du har [Node. js](https://nodejs.org/en/download/) installerat och skapa sedan en mapp som är värd för ditt program.
>
> ### <a name="option-2-visual-studio"></a>Alternativ 2: Visual Studio
> Följ dessa steg om du använder Visual Studio och skapar ett nytt projekt:
> 1. Välj **Arkiv** > **Nytt** > **Projekt** i Visual Studio.
> 1. Under **Visual C#\Web** väljer du **ASP.NET-webbprogram (.NET Framework)** .
> 1. Ange ett namn för ditt program och välj sedan **OK**.
> 1. Under **nytt ASP.NET-webbprogram**väljer du **Tom**.

## <a name="create-the-spa-ui"></a>Skapa SPA-ANVÄNDARGRÄNSSNITTET
1. Skapa en *index. html-* fil för Java Script Spa. Om du använder Visual Studio väljer du projektet (rotmappen för projektet). Högerklicka på och välj **Lägg till** > **nytt objekt** > **HTML-sida**och ge filen *index. html*ett namn.

1. I filen *index. html* lägger du till följande kod:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Du kan ersätta versionen av MSAL. js i skriptet ovan med den senaste utgivna versionen under [MSAL. js-versioner](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Använd Microsoft Authentication Library (MSAL) för att logga in användaren

Lägg till följande kod i `index.html`-filen inom `<script></script>`-taggarna:

   ```JavaScript
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

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Mer information

När en användare väljer knappen **Logga in** för första gången anropar `signIn` metod `loginPopup` för att logga in användaren. Den här metoden öppnar ett popup-fönster med *Microsoft Identity Platform-slutpunkten* för att fråga och verifiera användarens autentiseringsuppgifter. Efter en lyckad inloggning omdirigeras användaren tillbaka till den ursprungliga *index. html-* sidan. En token tas emot, bearbetas av `msal.js`och informationen som finns i token cachelagras. Denna token kallas *ID-token* och innehåller grundläggande information om användaren, t. ex. användarens visnings namn. Om du planerar att använda data som tillhandahålls av denna token i något syfte, måste du se till att denna token verifieras av backend-servern för att garantera att token har utfärdats till en giltig användare för ditt program.

Det SPA-skydd som genereras av den här guiden anropar `acquireTokenSilent` och/eller `acquireTokenPopup` för att *Hämta en åtkomsttoken som används* för att skicka frågor till Microsoft Graph-API för användar profil information. Ta en titt på [det här](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-dotNet-WebAPI-v2-exempel") exempel programmet i GitHub om du behöver ett exempel som VALIDERAR ID-token. Exemplet använder ett ASP.NET webb-API för verifiering av token.

#### <a name="getting-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Efter den första inloggningen vill du inte be användarna att autentisera varje gång de behöver för att begära en token för att få åtkomst till en resurs. Så *acquireTokenSilent* bör användas mest av tiden för att hämta tokens. Det finns dock situationer där du måste tvinga användare att interagera med Microsoft Identity Platform-slutpunkten. Till exempel:

- Användare måste ange sina autentiseringsuppgifter på grund av att lösen ordet har upphört att gälla.
- Ditt program begär åtkomst till en resurs och du behöver användarens medgivande.
- Tvåfaktorautentisering krävs.

När du anropar *acquireTokenPopup* öppnas ett popup-fönster (eller *acquireTokenRedirect* omdirigerar användare till Microsoft Identity Platform-slutpunkten). I det fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge tillåtelse till den begärda resursen eller utföra tvåfaktorautentisering.

#### <a name="getting-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar hämtning av token och förnyelse utan någon användar interaktion. När `loginPopup` (eller `loginRedirect`) körs för första gången är `acquireTokenSilent` den metod som används ofta för att hämta tokens som används för att få åtkomst till skyddade resurser för efterföljande anrop. (Anrop till begäran eller förnya token görs i bakgrunden.) `acquireTokenSilent` kan Miss lyckas i vissa fall. Användarens lösen ord kan till exempel ha upphört att gälla. Ditt program kan hantera detta undantag på två sätt:

1. Gör ett anrop till `acquireTokenPopup` omedelbart, vilket utlöser en användar inloggnings uppmaning. Det här mönstret används ofta i online-program där det inte finns något oautentiserat innehåll i programmet som är tillgängligt för användaren. I det exempel som genereras av den här guidade installationen används det här mönstret.

2. Program kan också göra en visuell indikation på användaren om att en interaktiv inloggning krävs, så att användaren kan välja rätt tid för att logga in, eller så kan programmet försöka igen `acquireTokenSilent` vid ett senare tillfälle. Detta används vanligt vis när användaren kan använda andra funktioner i programmet utan att störas. Det kan till exempel finnas oautentiserat innehåll tillgängligt i programmet. I så fall kan användaren bestämma när de vill logga in för att komma åt den skyddade resursen eller uppdatera den inaktuella informationen.

> [!NOTE]
> I den här snabb starten används `loginRedirect` och `acquireTokenRedirect` metoder när Internet Explorer används i webbläsaren. Vi följer den här metoden på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som rör hur Internet Explorer hanterar popup-fönster i Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Anropa Microsoft Graph-API: et genom att använda den token som du nyss hämtade

Lägg till följande kod i `index.html`-filen inom `<script></script>`-taggarna:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddat API

I det exempel program som skapats i den här hand boken används metoden `callMSGraph()` för att göra en HTTP-`GET` begäran mot en skyddad resurs som kräver en token. Begäran returnerar sedan innehållet till anroparen. Den här metoden lägger till den hämtade token i *http-Authorization-huvudet*. För det exempel program som skapas av den här hand boken är resursen den Microsoft Graph API: t *mig* -slutpunkt, som visar användarens profil information.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

Lägg till följande kod i `index.html`-filen inom `<script></script>`-taggarna:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Registrera ditt program

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Om ditt konto ger dig åtkomst till fler än en klient väljer du kontot längst upp till höger och anger sedan din portal-session till den Azure AD-klient som du vill använda.
1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. I avsnittet **omdirigerings-URI** väljer du **webb** plattformen i list rutan och anger sedan värdet till den program-URL som är baserad på din webb server.

   Information om hur du ställer in och hämtar omdirigerings-URL: en för Node. js och Visual Studio finns i avsnittet "Ange en omdirigerings-URL för Node. js" och [Ange en omdirigerings-URL för Visual Studio](#set-a-redirect-url-for-visual-studio).

1. Välj **Registrera**.
1. På sidan **Översikt över** appar noterar du **programmets (klient) ID-** värde för senare användning.
1. Den här snabbstarten kräver att [flödet för implicit beviljande](v2-oauth2-implicit-grant-flow.md) aktiveras. I det vänstra fönstret i det registrerade programmet väljer du **autentisering**.
1. I **Avancerade inställningar**, under **implicit tilldelning**, markerar du kryss rutorna **ID-token** och **åtkomst-token** . ID-token och åtkomsttoken krävs eftersom den här appen måste logga in användare och anropa ett API.
1. Välj **Spara**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Ange en omdirigerings-URL för Node. js
> För Node. js kan du ange webb Server porten i filen *Server. js* . I den här självstudien används port 30662, men du kan använda någon annan tillgänglig port.
>
> Om du vill konfigurera en omdirigerings-URL i program registrerings informationen växlar du tillbaka till fönstret för **program registrering** och gör något av följande:
>
> - Ange *`http://localhost:30662/`* som **omdirigerings-URL**.
> - Om du använder en anpassad TCP-port använder du *`http://localhost:<port>/`* (där *\<port >* är det anpassade TCP-portnumret).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Ange en omdirigerings-URL för Visual Studio
> Följ dessa steg om du vill hämta omdirigerings-URL: en för Visual Studio:
> 1. I Solution Explorer väljer du projektet.
>
>    Fönstret **Egenskaper** öppnas. Om den inte gör det trycker du på F4.
>
>    ![JavaScriptSPA-projektet Fönstret Egenskaper](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Kopiera **URL** -värdet.
> 1. Växla tillbaka till fönstret **program registrering** och klistra in det kopierade värdet som **omdirigerings-URL**.

#### <a name="configure-your-javascript-spa"></a>Konfigurera ditt Java Script SPA

1. Lägg till program registrerings informationen i filen *index. html* som du skapade under projekt installationen. Lägg till följande kod högst upp i filen i `<script></script>` Taggar:

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

    Var:
    - *\<Enter_the_Application_Id_here >* är **program-ID: t (Client)** för det program som du har registrerat.
    - *\<Enter_the_Tenant_info_here >* har angetts till något av följande alternativ:
       - Om ditt program har stöd *för konton i den här organisations katalogen*ersätter du värdet med **klient-ID** eller **klient namn** (till exempel *contoso.Microsoft.com*).
       - Om ditt program har stöd *för konton i en organisations katalog*ersätter du värdet med **organisationer**.
       - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton*ersätter du värdet med **vanligt**. Om du bara vill begränsa stödet till *personliga Microsoft-konton*ersätter du värdet med **konsumenter**.

## <a name="test-your-code"></a>Testa koden

Testa din kod med någon av följande miljöer.

### <a name="test-with-nodejs"></a>Testa med Node. js

Om du inte använder Visual Studio kontrollerar du att webb servern har startats.

1. Konfigurera servern så att den lyssnar på en TCP-port som är baserad på platsen för din *index. html-* fil. För Node. js startar du webb servern för att lyssna på porten genom att köra följande kommandon i kommando tolken från programmappen:

    ```bash
    npm install
    node server.js
    ```
1. I webbläsaren skriver du **http://\<span >\</span > localhost: 30662** eller **http://\<span >\</span > localhost: {port}** , där *port* är den port som webb servern lyssnar på. Du bör se innehållet i filen *index. html* och knappen **Logga in** .

### <a name="test-with-visual-studio"></a>Testa med Visual Studio

Om du använder Visual Studio väljer du projekt lösningen och trycker sedan på F5 för att köra projektet. Webbläsaren öppnas till platsen http://<span></span>localhost: {port} och knappen **Logga in** ska vara synlig.

## <a name="test-your-application"></a>Testa ditt program

När webbläsaren har läst in filen *index. html* väljer du **Logga**in. Du uppmanas att logga in med Microsoft Identity Platform-slutpunkten:

![Inloggnings fönstret för Java scripts SPA-konto](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Ge tillstånd för program åtkomst

Första gången du loggar in på ditt program uppmanas du att ge den åtkomst till din profil och logga in dig:

![Fönstret "behörigheter begärd"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visa program resultat

När du har loggat in returneras din användar profil information i det Microsoft Graph API-svar som visas:

![Resultat från API-anropet Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om omfattningar och delegerade behörigheter

Microsoft Graph-API: t kräver att *User. Read* -omfånget läser en användar profil. Som standard läggs det här omfånget automatiskt till i varje program som är registrerat på registrerings portalen. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare omfång. Till exempel kräver Microsoft Graph-API: n *kalendrar. Läs* omfattning för att lista användarens kalendrar.

Om du vill komma åt användarens kalendrar i ett programs kontext lägger du till *kalendrarna. Läs* behörighet för program registrerings informationen. Lägg sedan till *kalendrarna. Läs* omfattning i `acquireTokenSilent`-anropet.

>[!NOTE]
>Användaren kan tillfrågas om ytterligare medgivanden när du ökar antalet omfång.

Om ett Server dels-API inte kräver ett omfång (rekommenderas inte), kan du använda *clientId* som omfånget i anropen för att hämta tokens.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
