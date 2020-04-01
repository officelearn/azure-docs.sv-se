---
title: Självstudiekurs för JavaScript-appar - Microsoft identity platform | Azure
description: Så här kan JavaScript SPA-program anropa ett API som kräver åtkomsttoken av Azure Active Directory v2.0-slutpunkten
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
ms.openlocfilehash: 6eb144e648e8f5fa1682c353f14686d6f82c7328
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79530452"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Logga in användare och anropa Microsoft Graph API från ett javascript-ensidigt program (SPA)

Den här guiden visar hur ett javascript-program (SPA) kan:
- Logga in personliga konton samt arbets- och skolkonton 
- Hämta en åtkomsttoken
- Anropa Microsoft Graph API eller andra API:er som kräver åtkomsttoken från slutpunkten för *Microsoft-identitetsplattformen*

>[!NOTE]
> Om du inte har tillgång till Microsofts identitetsplattform tidigare rekommenderar vi att du börjar med [inloggningsanvändarna och får en åtkomsttoken i en Snabbstart för JavaScript SPA.](quickstart-v2-javascript.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelappen som genereras av den här guiden

![Visar hur exempelappen som genereras av den här självstudien fungerar](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Exempelprogrammet som skapas av den här guiden gör det möjligt för ett JavaScript SPA att fråga Microsoft Graph API eller ett webb-API som accepterar token från slutpunkten för Microsoft-identitetsplattformen. I det här fallet när en användare loggar in, begärs en åtkomsttoken och läggs till HTTP-begäranden via auktoriseringshuvudet. Den här token används för att hämta användarens profil och e-post via **MS Graph API**. Tokenförvärv och förnyelse hanteras av **Microsoft Authentication Library (MSAL) för JavaScript**.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library för JavaScript|

> [!NOTE]
> *Msal.js* riktar in sig på slutpunkten för Microsoft identity platform, som gör det möjligt för personliga konton och skol- och arbetskonton att logga in och hämta token. Slutpunkten för Microsoft-identitetsplattformen har [vissa begränsningar](../azuread-dev/azure-ad-endpoint-comparison.md#limitations).
> För att förstå skillnaderna mellan v1.0- och v2.0-slutpunkterna finns i [jämförelseguiden för slutpunkt](../azuread-dev/azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Konfigurera webbservern eller webbprojektet

> Föredrar du att ladda ned det här exemplets projekt istället? [Ladda ner projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Om du vill konfigurera kodexemplet innan du kör det går du till [konfigurationssteget](#register-your-application).

## <a name="prerequisites"></a>Krav

* Om du vill köra den här självstudien behöver du en lokal webbserver, till exempel [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core)eller IIS Express-integrering med [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Instruktionerna i den här guiden baseras på en webbserver som är inbyggd i Node.js. Vi rekommenderar att du använder [Visual Studio Code](https://code.visualstudio.com/download) som din integrerade utvecklingsmiljö (IDE).

## <a name="create-your-project"></a>Skapa ditt projekt

Kontrollera att du har [nod.js](https://nodejs.org/en/download/) installerat och skapa sedan en mapp som är värd för ditt program. Där kommer vi att [Express](https://expressjs.com/) implementera en enkel `index.html` Express webbserver för att tjäna din fil. 

1. Först med integrerad Visual Studio Code-terminal, leta reda på projektmappen och installera sedan Express med NPM.

1. Skapa sedan en .js-fil med namnet `server.js`och lägg sedan till följande kod:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

Du har nu en enkel server för att tjäna ditt SPA. Den avsedda mappstrukturen i slutet av den här självstudien är följande:

![en textskildring av den avsedda SPA-mappstrukturen](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Skapa SPA-användargränssnittet

1. Skapa `index.html` en fil för ditt JavaScript SPA. Den här filen implementerar ett användargränssnitt byggt med **Bootstrap 4 Framework** och importerar skriptfiler för konfiguration, autentisering och API-anrop.

   Lägg `index.html` till följande kod i filen:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > Du kan ersätta versionen av MSAL.js i det föregående skriptet med den senast utgivna versionen under [MSAL.js-versioner](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
   
2. Nu skapar du en .js-fil med namnet `ui.js`, som kommer åt och uppdaterar DOM-element, och lägger till följande kod:

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

Innan du fortsätter med autentiseringen registrerar du ditt program på **Azure Active Directory**.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Om ditt konto ger dig åtkomst till mer än en klient väljer du kontot längst upp till höger och ställer sedan in portalsessionen på den Azure AD-klient som du vill använda.
1. Gå till sidan Microsoft identity platform for developers [App registrations.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **Kontotyper som stöds** väljer du **Accounts in any organizational directory and personal Microsoft accounts** (Konton i alla organisationskataloger och personliga Microsoft-konton).
1. I avsnittet **Omdirigera URI** väljer du **webbplattformen** i listrutan och anger sedan värdet till program-URL:en som baseras på webbservern.
1. Välj **Registrera**.
1. På sidan Översikt **över** appen noterar du **värdet program (klient)** för senare användning.
1. Den här snabbstarten kräver att [flödet för implicit beviljande](v2-oauth2-implicit-grant-flow.md) aktiveras. Välj **Autentisering**i den vänstra rutan i det registrerade programmet .
1. I **Avancerade inställningar**under Implicit **beviljande**markerar du **kryssrutorna ID-token** och **Access-token.** ID-token och åtkomsttoken krävs eftersom den här appen måste logga in användare och anropa ett API.
1. Välj **Spara**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Ange en omdirigerings-URL för Node.js
>
> För Node.js kan du ange webbserverporten i *filen server.js.* Den här självstudien använder port 3000, men du kan använda alla andra tillgängliga portar.
>
> Om du vill konfigurera en url för omdirigering i programregistreringsinformationen växlar du tillbaka till fönstret **Programregistrering** och gör något av följande:
>
> - Ange *`http://localhost:3000/`* som **url för omdirigering**.
> - Om du använder en anpassad TCP-port använder du *`http://localhost:<port>/`* (där * \<port>* är det anpassade TCP-portnumret).
>   1. Kopiera **URL-värdet.**
>   1. Växla tillbaka till **fönstret Programregistrering** och klistra in det kopierade värdet som **url för omdirigering**.
>

### <a name="configure-your-javascript-spa"></a>Konfigurera ditt JavaScript SPA

Skapa en ny .js-fil med namnet `authConfig.js`, som innehåller dina konfigurationsparametrar för autentisering, och lägg till följande kod:

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
    scopes: ["Mail.Read"]
  };
```

 Där:
 - Enter_the_Application_Id_Here>är **programmets (klient)-ID** för det program du registrerade. * \<*
 - Enter_the_Cloud_Instance_Id_Here>är instansen av Azure-molnet. * \<* För huvud- eller globala Azure-molnet anger du *https://login.microsoftonline.com*bara . För **nationella** moln (till exempel Kina), se [Nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Enter_the_Tenant_info_here>är inställt på något av följande alternativ: * \<*
   - Om ditt program stöder *konton i den här organisationskatalogen*ersätter du det här värdet med **klient-ID: n** eller **klientnamnet** (till exempel *contoso.microsoft.com*).
   - Om programmet stöder *konton i en organisationskatalog* **ersätter**du det här värdet med organisationer .
   - Om programmet stöder *konton i en organisationskatalog och personliga Microsoft-konton*ersätter du det här värdet med **vanliga**. Om du bara vill begränsa supporten till *personliga Microsoft-konton*ersätter du det här värdet med **konsumenter**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Använda Microsoft Authentication Library (MSAL) för att logga in användaren

Skapa en ny .js-fil med namnet `authPopup.js`, som innehåller autentiserings- och tokeninsamlingslogiken, och lägg till följande kod:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
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
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Mer information

När en användare har valt knappen **Logga** in `signIn` för `loginPopup` första gången anropas metoden för att logga in användaren. Den här metoden öppnar ett popup-fönster med *slutpunkten* för Microsoft identity platform för att fråga och validera användarens autentiseringsuppgifter. Efter en lyckad inloggning omdirigeras användaren tillbaka till den ursprungliga *index.html-sidan.* En token tas emot, `msal.js`bearbetas av och informationen i token cachelagras. Den här token kallas *ID-token* och innehåller grundläggande information om användaren, till exempel användarnamnet. Om du planerar att använda data som tillhandahålls av den här token för något ändamål måste du se till att den här token valideras av serverdservern för att garantera att token har utfärdats till en giltig användare för ditt program.

Spa-åtkomstanrop som `acquireTokenSilent` genereras `acquireTokenPopup` av den här handboken anropar och/eller hämtar en *åtkomsttoken* som används för att fråga microsoft graph-API:et efter användarprofilinformation. Om du behöver ett exempel som validerar ID-token kan du ta en titt på [det här](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 exempel") exempelprogrammet i GitHub. I exemplet används ett ASP.NET webb-API för tokenverifiering.

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Efter den första inloggningen vill du inte be användarna att omauktorisera varje gång de behöver begära en token för att komma åt en resurs. Så *acquireTokenSilent* bör användas för det mesta för att förvärva tokens. Det finns dock situationer där du måste tvinga användare att interagera med Slutpunkten för Microsoft identity platform. Exempel på rekommendationer:

- Användarna måste ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla.
- Ditt program begär åtkomst till en resurs och du behöver användarens samtycke.
- Tvåfaktorsautentisering krävs.

Anropar *acquireTokenPopup* öppnar ett popup-fönster (eller *förvärvarTokenRedirect* omdirigerar användare till slutpunkten för Microsoft-identitetsplattformen). I det fönstret måste användarna interagera genom att bekräfta sina autentiseringsuppgifter, ge medgivande till den nödvändiga resursen eller slutföra tvåfaktorsautentiseringen.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar tokenanskaffning och förnyelse utan någon användarinteraktion. När `loginPopup` (eller `loginRedirect`) körs för första `acquireTokenSilent` gången, är den metod som vanligen används för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop. (Anrop för att begära eller förnya token görs tyst.) `acquireTokenSilent` kan misslyckas i vissa fall. Användarens lösenord kan till exempel ha upphört att gälla. Ditt program kan hantera det här undantaget på två sätt:

1. Ring ett `acquireTokenPopup` samtal till omedelbart, vilket utlöser en användares inloggningsfråga. Det här mönstret används ofta i onlineprogram där det inte finns något oautenterat innehåll i programmet tillgängligt för användaren. Exemplet som genereras av den här guidade inställningen använder det här mönstret.

1. Program kan också göra en visuell indikation till användaren att en interaktiv inloggning krävs, så att användaren kan `acquireTokenSilent` välja rätt tid att logga in, eller programmet kan försöka igen vid ett senare tillfälle. Detta används ofta när användaren kan använda andra funktioner i programmet utan att störas. Det kan till exempel finnas oautentiserade innehåll som är tillgängligt i programmet. I det här fallet kan användaren bestämma när de vill logga in för att komma åt den skyddade resursen eller uppdatera den inaktuella informationen.

> [!NOTE]
> Den här snabbstarten använder metoderna `loginPopup` och `acquireTokenPopup` som standard. Om du använder Internet Explorer som webbläsare rekommenderas `loginRedirect` `acquireTokenRedirect` att använda och metoder på grund av ett [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som rör hur popup-fönster hanteras i Internet Explorer. Om du vill se hur du uppnår `Redirect methods`samma resultat med , [se](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js). 
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Anropa Microsoft Graph API med hjälp av den token du just fått

1. Skapa först en .js-fil med namnet `graphConfig.js`, som lagrar REST-slutpunkterna. Lägg till följande kod:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Där:
   - Enter_the_Graph_Endpoint_Here>är instansen av MS Graph API. * \<* För den globala MS Graph API-slutpunkten `https://graph.microsoft.com`ersätter du bara den här strängen med . Nationella molndistributioner finns i [Graph API-dokumentation](https://docs.microsoft.com/graph/deployments).

1. Skapa sedan en .js-fil med namnet `graph.js`, som ska göra ett REST-anrop till Microsoft Graph API, och lägga till följande kod:

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddat API

I exempelprogrammet som skapats `callMSGraph()` av den här guiden `GET` används metoden för att göra en HTTP-begäran mot en skyddad resurs som kräver en token. Begäran returnerar sedan innehållet till anroparen. Den här metoden lägger till den förvärvade token i *HTTP-auktoriseringshuvudet*. För exempelprogrammet som skapats av den här guiden är resursen slutpunkten Microsoft Graph API *me,* som visar användarens profilinformation.

<!--end-collapse-->

## <a name="test-your-code"></a>Testa koden

1. Konfigurera servern så att den lyssnar på en TCP-port som baseras på platsen för *filen index.html.* För Node.js startar du webbservern för att lyssna på porten genom att köra följande kommandon vid en kommandoradsfråga från programmappen:

   ```bash
   npm install
   npm start
   ```
1. I webbläsaren anger **http://localhost:3000** **http://localhost:{port}** eller , där *porten* är den port som webbservern lyssnar på. Du bör se innehållet i filen *index.html* och **inloggningsknappen.**

## <a name="test-your-application"></a>Testa ditt program

När webbläsaren har läser in filen *index.html* väljer du **Logga in**. Du uppmanas att logga in med slutpunkten för Microsoft identity platform:

![Inloggningsfönstret för JavaScript SPA-konto](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Ge samtycke till tillgång till program

Första gången du loggar in på ditt program uppmanas du att ge den åtkomst till din profil och logga in dig:

![Fönstret "Begärda behörigheter"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visa programresultat

När du har loggat in returneras din användarprofilinformation i Microsoft Graph API-svar som visas:

![Resultat från Microsoft Graph API-anrop](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver *att användaren.läser* omfång för att läsa en användares profil. Som standard läggs det här scopet automatiskt till i alla program som är registrerade på registreringsportalen. Andra API:er för Microsoft Graph, samt anpassade API:er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver till exempel *e-post.Läs-scopet* för att kunna visa användarens e-post.

> [!NOTE]
> Användaren kan uppmanas att ange ytterligare medgivanden när du ökar antalet scope.

Om ett backend-API inte kräver ett scope (rekommenderas inte) kan du använda *clientId* som scope i anropen för att hämta token.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Hjälp oss att förbättra Microsofts identitetsplattform. Berätta vad du tycker genom att fylla i en kort två-fråga undersökning.

> [!div class="nextstepaction"]
> [Undersökning av Microsofts identitetsplattform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
