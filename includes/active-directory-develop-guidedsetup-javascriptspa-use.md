---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 68598d4bb7fb9fd928a7b664e6ce0c02220ca4bb
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503176"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Använd Microsoft Authentication Library (MSAL) för att logga in användaren

1. Lägg till följande kod till din `index.html` filen inom den `<script></script>` taggar:

```javascript
//Pass null for default authority (https://login.microsoftonline.com/common)
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack,
    {storeAuthStateInCookie: true, cacheLocation: "localStorage"});

function signIn() {
    myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
        //Login Success
        showWelcomeMessage();
        acquireTokenPopupAndCallMSGraph();
    }, function (error) {
        console.log(error);
    });
}

function acquireTokenPopupAndCallMSGraph() {
    //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
    myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
        callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
    }, function (error) {
        console.log(error);
        // Call acquireTokenPopup (popup window) in case of acquireTokenSilent failure due to consent or interaction required ONLY
        if (error.indexOf("consent_required") !== -1 || error.indexOf("interaction_required") !== -1 || error.indexOf("login_required") !== -1) {
            myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
                callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
            }, function (error) {
                console.log(error);
            });
        }
    });
}

function graphAPICallback(data) {
    //Display user data on DOM
    var divWelcome = document.getElementById('WelcomeMessage');
    divWelcome.innerHTML += " to Microsoft Graph API!!";
    document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
}

function showWelcomeMessage() {
    var divWelcome = document.getElementById('WelcomeMessage');
    divWelcome.innerHTML += 'Welcome ' + myMSALObj.getUser().name;
    var loginbutton = document.getElementById('SignIn');
    loginbutton.innerHTML = 'Sign Out';
    loginbutton.setAttribute('onclick', 'signOut();');
}

// This function can be removed if you do not need to support IE
function acquireTokenRedirectAndCallMSGraph() {
    //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
    myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
      callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
    }, function (error) {
        console.log(error);
        //Call acquireTokenRedirect in case of acquireToken Failure
        if (error.indexOf("consent_required") !== -1 || error.indexOf("interaction_required") !== -1 || error.indexOf("login_required") !== -1) {
            myMSALObj.acquireTokenRedirect(applicationConfig.graphScopes);
        }
    });
}

function acquireTokenRedirectCallBack(errorDesc, token, error, tokenType) {
    if (tokenType === "access_token") {
        callMSGraph(applicationConfig.graphEndpoint, token, graphAPICallback);
    } else {
        console.log("token type is:"+tokenType);
    }
}


// Browser check variables
var ua = window.navigator.userAgent;
var msie = ua.indexOf('MSIE ');
var msie11 = ua.indexOf('Trident/');
var msedge = ua.indexOf('Edge/');
var isIE = msie > 0 || msie11 > 0;
var isEdge = msedge > 0;

//If you support IE, our recommendation is that you sign-in using Redirect APIs
//If you as a developer are testing using Microsoft Edge InPrivate mode, please add "isEdge" to the if check
if (!isIE) {
    if (myMSALObj.getUser()) {// avoid duplicate code execution on page load in case of iframe and popup window.
        showWelcomeMessage();
        acquireTokenPopupAndCallMSGraph();
    }
} else {
    document.getElementById("SignIn").onclick = function () {
        myMSALObj.loginRedirect(applicationConfig.graphScopes);
    };
    if (myMSALObj.getUser() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
        showWelcomeMessage();
        acquireTokenRedirectAndCallMSGraph();
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Mer information

När en användare klickar på **logga In** knappen för första gången den `signIn` metodanrop `loginPopup` att logga in användaren. Den här metoden innebär att öppna ett popup-fönster med de *Microsoft identity-plattformen endpoint* att fråga efter och verifiera användarens autentiseringsuppgifter. Till följd av en lyckad inloggning, användaren omdirigeras tillbaka till ursprungligt *index.html* sidan och en token tas emot, bearbetas av `msal.js` och den information som finns i token cachelagras. Denna token kallas den *ID-token* och innehåller grundläggande information om användare, till exempel användarens visningsnamn. Om du planerar att använda några data som tillhandahålls av denna token ändamål, måste du kontrollera att denna token ska valideras av backend-servern för att garantera att token utfärdats till en giltig användare för ditt program.

SPA som genereras av det här hjälper anrop `acquireTokenSilent` och/eller `acquireTokenPopup` att hämta en *åtkomsttoken* används för att fråga Microsoft Graph API för profilinformation för användaren. Om du behöver ett exempel som validerar ID-token kan ta en titt på [detta](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 exempel") exempelprogrammet i GitHub – exemplet använder en ASP .NET webb-API för tokenvalidering.

#### <a name="getting-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Efter den första inloggningen, du inte vill be användare att autentiseras på nytt varje gång som de behöver för att begära en token till en resurs – så *acquireTokenSilent* bör användas för de flesta fall för att hämta token. Det finns dock situationer när måste du tvinga användare att interagera med Microsoft identity-plattformen endpoint – några exempel är:

- Användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- Din app begär åtkomst till en resurs som användaren behöver ge sitt medgivande för
- Tvåfaktorsautentisering krävs

Anropa den *acquireTokenPopup(scope)* resulterar i ett popup-fönster (eller *acquireTokenRedirect(scope)* resulterar i att omdirigera användare till Microsoft identity-plattformen slutpunkten) där användare måste interagera genom att antingen bekräfta sina autentiseringsuppgifter, ge samtycke till den begärda resursen eller du har slutfört tvåfaktorautentisering.

#### <a name="getting-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar hämtning och förnyelse av token utan någon användarinteraktion. Efter `loginPopup` (eller `loginRedirect`) körs för första gången `acquireTokenSilent` är den metod som ofta används för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop - eftersom anrop till begära eller förnya token görs tyst.
`acquireTokenSilent` kanske inte kan utföras i vissa fall – till exempel användarens lösenord har upphört att gälla. Programmet kan hantera det här undantaget på två sätt:

1. Göra ett anrop till `acquireTokenPopup` direkt, vilket innebär att uppmanar användaren att logga in. Det här mönstret används ofta i online-program där det finns inget oautentiserade innehåll i programmet tillgängligt för användaren. I exemplet som genererats av den här guidade konfigurationen används det här mönstret.

2. Program kan också göra en visuell indikering för användaren som en interaktiv inloggning krävs, så att användaren kan välja rätt tid att logga in eller programmet kan försöka `acquireTokenSilent` vid ett senare tillfälle. Detta används vanligtvis när användaren kan använda andra funktioner i programmet utan störs – till exempel finns oautentiserade innehåll i programmet. I det här fallet kan användaren avgöra när de vill logga in till den skyddade resursen eller uppdatera gammal information.

> [!NOTE]
> Koden ovan använder den `loginRedirect` och `acquireTokenRedirect` metoder när webbläsaren som används är Internet Explorer på grund av en [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) rör hantering av popup-fönster genom att webbläsaren Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph API med hjälp av den token som du precis fick

Lägg till följande kod till din `index.html` filen inom den `<script></script>` taggar:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddade API

I det exempelprogram som skapats av den här guiden, den `callMSGraph()` metod för att göra HTTP `GET` begäran mot en skyddad resurs som kräver ett token och återgå sedan innehållet till anroparen. Den här metoden lägger till förvärvade token i den *HTTP auktoriseringsrubrik*. För exempelprogrammet som skapats av den här guiden, resursen är Microsoft Graph API *mig* slutpunkt – som visar användarens profilinformation.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

Lägg till följande kod till din `index.html` filen inom den `<script></script>` taggar:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
