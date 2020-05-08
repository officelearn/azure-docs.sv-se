---
title: Själv studie kurs om JavaScript-program med en enda sida 2,0-Microsoft Identity Platform | Azure
description: Hur Java Script SPA-program kan använda kod flödet för autentisering för att anropa ett API som kräver åtkomsttoken med hjälp av Azure Active Directory v 2.0-slutpunkt
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f664f5fa4219a8bcc32230b352e90cc2516faceb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890387"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>Logga in användare och anropa Microsoft Graph-API: et från ett Java Script-program med en enda sida (SPA)-MSAL. js 2,0

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av den här funktionen kan ändras före allmän tillgänglighet (GA).

I den här självstudien används en version av MSAL. js som använder OAuth 2,0-Authorization Code Flow med PKCE. Om du vill läsa mer om det här protokollet, samt skillnaderna mellan implicit flöde och kod flöde för auktoriseringskod, se [dokumentationen](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow). Om du letar efter en själv studie kurs som använder det implicita flödet kan du läsa [självstudien om MSAL. js v1](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa).

Den här versionen av MSAL. js förbättrar det aktuella biblioteket för MSAL-kärnan och använder sedan kod flödet för auktorisering i webbläsaren. De flesta funktioner som är tillgängliga i det gamla biblioteket är tillgängliga i den här versionen, men det finns olika delarna till autentiseringsschemat i båda. Den här versionen stöder **inte** det implicita flödet.

Den här guiden visar hur ett Java Script (Single-Side Application) kan:
- Logga in personliga konton, samt arbets-och skol konton
- Hämta en åtkomsttoken
- Anropa API: et för Microsoft Graph eller andra API: er som kräver åtkomsttoken från *Microsoft Identity Platform-slutpunkten*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hur exempel appen som genereras av den här hand boken fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Mer information

Det exempel program som skapats av den här guiden gör det möjligt för ett Java Script SPA att fråga Microsoft Graph-API eller ett webb-API som accepterar token från Microsoft Identity Platform-slutpunkten. I det här scenariot begärs en åtkomsttoken efter att användaren loggar in och läggs till i HTTP-begäranden via Authorization-huvudet. Hämtning av token och förnyelse hanteras av Microsoft Authentication Library (MSAL).

### <a name="libraries"></a>Bibliotek

I den här guiden används följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Microsoft Authentication Library för Java Script Browser-paket|

## <a name="set-up-your-web-server-or-project"></a>Konfigurera din webb server eller ditt projekt

Vill du ladda ned det här exemplets projekt i stället? För att köra projektet med hjälp av en lokal webb server, till exempel Node. js, klona projektfilerna:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Om du vill konfigurera kod exemplet innan du kör det går du vidare till [konfigurations steget](#register-your-application).

## <a name="prerequisites"></a>Krav

* För att kunna köra den här självstudien behöver du en lokal webb server, till exempel [Node. js](https://nodejs.org/en/download/) eller [.net Core](https://www.microsoft.com/net/core).

* Om du använder Node. js för att köra projektet installerar du en Integrated Development Environment (IDE) som [Visual Studio Code](https://code.visualstudio.com/download) för att redigera projektfilerna.

* Anvisningarna i den här självstudien baseras på Node. js.

## <a name="create-your-project"></a>Skapa ditt projekt

Kontrol lera att du har [Node. js](https://nodejs.org/en/download/) installerat och skapa sedan en mapp som är värd för ditt program. Implementera sedan en liten [Express](https://expressjs.com/) -webbserver för att hantera din `index.html` fil.

1. Börja med att navigera till projektmappen i terminalen och kör sedan följande NPM-kommandon.
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. Skapa därefter en. js-fil med namnet *Server. js*och Lägg sedan till följande kod:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Nu har du en enkel server som kan hantera din SPA. Den avsedda mappstrukturen i slutet av den här självstudien är följande:

![en text som beskriver den avsedda SPA-mappstrukturen](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Skapa SPA-ANVÄNDARGRÄNSSNITTET

1. Skapa en *index. html-* fil för Java Script Spa i *app* -mappen. Den här filen implementerar ett användar gränssnitt som skapats med **bootstrap 4 Framework** och importerar skriptfiler för konfigurations-, autentiserings-och API-anrop.

   I filen *index. html* lägger du till följande kod:

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
         <br>
         <div class="row" style="margin:auto" >
         <div id="card-div" class="col-md-3" style="display:none">
         <div class="card text-center">
            <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
            </div>
         </div>
         </div>
         <br>
         <br>
            <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
            </div>
            <div class="col-md-5">
             <div class="tab-content" id="nav-tabContent">
            </div>
            </div>
         </div>
         <br>
         <br>

         <!-- importing bootstrap.js and supporting js libraries -->
         <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
         <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
         <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

         <!-- importing app scripts (load order is important) -->
         <script type="text/javascript" src="./authConfig.js"></script>
         <script type="text/javascript" src="./graphConfig.js"></script>
         <script type="text/javascript" src="./ui.js"></script>

         <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
         <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
         <script type="text/javascript" src="./authPopup.js"></script>
         <script type="text/javascript" src="./graph.js"></script>
      </body>
     </html>
     ```

   > [!TIP]
   > Du kan ersätta versionen av MSAL. js i skriptet ovan med den senaste utgivna versionen under [MSAL. js-versioner](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


2. Skapa nu en. js-fil med namnet *UI. js* som kommer att få åtkomst till och uppdatera dom-element och Lägg till följande kod:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>Registrera ditt program

Följ anvisningarna för att [Registrera ett nytt program med en sida](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration).

#### <a name="set-a-redirect-url-for-nodejs"></a>Ange en omdirigerings-URL för Node. js

För Node. js kan du ange webb Server porten i filen *Server. js* . I den här självstudien används port 3000, men du kan använda någon annan tillgänglig port.

Om du vill konfigurera en omdirigerings-URL i program registrerings informationen växlar du tillbaka till fönstret för **program registrering** och registrerar en ny **Spa** med något av följande:

- Ange *`http://localhost:3000/`* som **omdirigerings-URL**.
- Om du använder en anpassad TCP-port använder *`http://localhost:<port>/`* du (där * \<port>* är det anpassade TCP-portnumret).

### <a name="configure-your-javascript-spa"></a>Konfigurera ditt Java Script SPA

Skapa en ny. js-fil med namnet *authConfig. js* som innehåller dina konfigurations parametrar för autentisering och Lägg till följande kod:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 Där:
 - Enter_the_Application_Id_Here>är **program-ID: t (Client)** för det program som du har registrerat. * \<*
 - Enter_the_Cloud_Instance_Id_Here>är instansen av Azure-molnet. * \<* För det största eller globala Azure-molnet anger *https://login.microsoftonline.com*du bara. För **nationella** moln (till exempel Kina), se [nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Enter_the_Tenant_info_here>har angetts till något av följande alternativ: * \<*
   - Om ditt program har stöd *för konton i den här organisations katalogen*ersätter du värdet med **klient-ID** eller **klient namn** (till exempel *contoso.Microsoft.com*).
   - Om ditt program har stöd *för konton i en organisations katalog*ersätter du värdet med **organisationer**.
   - Om ditt program har stöd *för konton i en organisations katalog och personliga Microsoft-konton*ersätter du värdet med **vanligt**. Om du bara vill begränsa stödet till *personliga Microsoft-konton*ersätter du värdet med **konsumenter**.
- *\ Enter_the_Redirect_Uri_Here>* är den port som du har registrerat på portalen*`http://localhost:3000/`*()


Skapa en ny. js-fil `graphConfig.js`med namnet, som innehåller dina konfigurations parametrar som anropar Microsoft Graph API och Lägg till följande kod:
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- Enter_the_Graph_Endpoint_Here>är instansen av MS Graph API. * \<* För den globala MS Graph API-slutpunkten ersätter du bara `https://graph.microsoft.com`den här strängen med. För nationella moln distributioner, se Graph API- [dokumentationen](https://docs.microsoft.com/graph/deployments).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Använd Microsoft Authentication Library (MSAL) för att logga in användaren

### <a name="popup"></a>Motta
Skapa en ny. js-fil `authPopup.js`med namnet, som innehåller din autentiserings-och token förvärvs logik för inloggnings-popUp och Lägg till följande kod:

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>Omdirigera
Skapa en ny. js-fil `authRedirect.js`med namnet, som innehåller din autentiserings-och token förvärvs logik för inloggnings omdirigering och Lägg till följande kod:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>Mer information

När en användare väljer knappen **Logga in** för första gången, anropar `signIn` `loginPopup` metoden för att logga in användaren. Den här metoden öppnar ett popup-fönster med *Microsoft Identity Platform-slutpunkten* för att fråga och verifiera användarens autentiseringsuppgifter. Efter en lyckad inloggning initierar *msal. js* [flödet för auktoriseringskod](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

I det här fallet skickas en PKCE till den CORS-skyddade token-slutpunkten och utbyts för token. En ID-token, åtkomsttoken och uppdaterad token tas emot, bearbetas av *msal. js*och informationen i token cachelagras.

ID-token innehåller grundläggande information om användaren, t. ex. visnings namnet. Om du planerar att använda data som tillhandahålls av denna token måste den verifieras av backend-servern för att garantera att token utfärdats till en giltig användare för ditt program. Uppdateringstoken har en begränsad livs längd och upphör att gälla efter 24 timmar. Uppdateringstoken kan användas för att tyst hämta nya åtkomsttoken.

Det SPA som genereras av den här `acquireTokenSilent` guiden anropar `acquireTokenPopup` och/eller *hämtar en åtkomsttoken som används* för att fråga Microsoft Graph-API: t för användar profil information. Om du behöver ett exempel som validerar ID-token, se exempel programmet [Active-Directory-JavaScript-singlepageapp-dotNet-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) på GitHub. Exemplet använder ett ASP.NET webb-API för verifiering av token.

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Efter den första inloggningen bör användarna inte uppmanas att autentisera varje gång de behöver för att begära en token för att få åtkomst till en resurs. Använd `acquireTokenSilent`för att förhindra sådana omautentiserings begär Anden. Det finns dock vissa situationer där du kan behöva tvinga användare att interagera med Microsoft Identity Platform-slutpunkten. Exempel:

- Användarna måste ange sina autentiseringsuppgifter på nytt eftersom lösen ordet har upphört att gälla.
- Ditt program begär åtkomst till en resurs och du behöver användarens medgivande.
- Tvåfaktorautentisering krävs.

Anrop `acquireTokenPopup` öppnar ett popup-fönster (eller `acquireTokenRedirect` omdirigerar användare till Microsoft Identity Platform-slutpunkten). I det fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge tillåtelse till den begärda resursen eller utföra tvåfaktorautentisering.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

`acquireTokenSilent` Metoden hanterar hämtning av token och förnyelsen utan några åtgärder från användaren. När `loginPopup` (eller `loginRedirect`) körs för första gången `acquireTokenSilent` är metoden ofta använd för att hämta tokens som används för att komma åt skyddade resurser för efterföljande anrop. (Anrop till begäran eller förnya token görs i bakgrunden.) `acquireTokenSilent` kan Miss lyckas i vissa fall. Användarens lösen ord kan till exempel ha upphört att gälla. Ditt program kan hantera detta undantag på två sätt:

1. Gör ett anrop till `acquireTokenPopup` omedelbart för att utlösa en användar inloggnings uppmaning. Det här mönstret används ofta i online-program där det inte finns något oautentiserat innehåll i programmet som är tillgängligt för användaren. I det exempel som genereras av den här guidade installationen används det här mönstret.

1. Visar visuellt för användaren att en interaktiv inloggning krävs så att användaren kan välja rätt tid för att logga in, eller så kan programmet försöka igen `acquireTokenSilent` vid ett senare tillfälle. Den här tekniken används ofta när användaren kan använda andra funktioner i programmet utan att störas. Det kan till exempel finnas oautentiserat innehåll tillgängligt i programmet. I så fall kan användaren bestämma när de vill logga in för att komma åt den skyddade resursen eller uppdatera den inaktuella informationen.

> [!NOTE]
> I den här snabb `loginPopup` starten `acquireTokenPopup` används som standard metoderna och. Om du använder Internet Explorer som webbläsare rekommenderar vi att du använder `loginRedirect` och `acquireTokenRedirect` metoder, på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som är relaterade till hur Internet Explorer hanterar popup-fönster. Om du vill se hur du uppnår samma resultat med omdirigerings metoder, se [*authRedirect. js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Anropa Microsoft Graph-API: et genom att använda den token som du nyss hämtade


 Skapa en. js-fil med namnet *Graph. js* som kommer att göra ett rest-anrop till Microsoft Graph API och Lägg till följande kod:

   ```javascript

// Helper function to call MS Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddat API

I det exempel program som skapats av den här guiden `callMSGraph()` används metoden för att göra en http `GET` -begäran mot en skyddad resurs som kräver en token. Begäran returnerar sedan innehållet till anroparen. Den här metoden lägger till den hämtade token i *http-Authorization-huvudet*. För det exempel program som skapas av den här hand boken är resursen den Microsoft Graph API: t *mig* -slutpunkt, som visar användarens profil information.

## <a name="test-your-code"></a>Testa koden

1. För Node. js startar du webb servern genom att köra följande kommandon inifrån programmappen:

   ```bash
   npm install
   npm start
   ```
1. I webbläsaren anger **http://localhost:3000** eller **http://localhost:{port}**, där *port* är den port som webb servern lyssnar på. Du bör se innehållet i filen *index. html* och knappen **Logga in** .

## <a name="test-your-application"></a>Testa ditt program

När webbläsaren har läst in filen *index. html* väljer du **Logga**in. Du uppmanas att logga in med Microsoft Identity Platform-slutpunkten:

![Inloggnings fönstret för Java scripts SPA-konto](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Ge tillstånd för program åtkomst

Första gången du loggar in på ditt program uppmanas du att ge den åtkomst till din profil och logga in dig:

![Fönstret "behörigheter begärd"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visa program resultat

När du har loggat in returneras din användar profil information i det Microsoft Graph API-svar som visas:

![Resultat från API-anropet Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om omfattningar och delegerade behörigheter

Microsoft Graph-API: t kräver att *User. Read* -omfånget läser en användar profil. Som standard läggs denna omfattning automatiskt till i varje program som är registrerat i Azure Portal. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare omfång. Till exempel kräver Microsoft Graph-API: t *e-post. Read-* omfånget för att kunna lista användarens e-post.

> [!NOTE]
> Användaren kan tillfrågas om ytterligare medgivande när du lägger till omfattningar.

Om ett Server dels-API inte kräver ett omfång (rekommenderas inte), kan du använda *clientId* som omfånget i anropen för att hämta tokens.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

[MSAL. js GitHub-lagrings platsen](https://github.com/AzureAD/microsoft-authentication-library-for-js) innehåller ytterligare biblioteks dokumentation, vanliga frågor och svar och ger support.
