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
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199509"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Använd Microsoft Authentication Library (MSAL) för att logga in användaren

1. Lägg till följande kod till din `index.html` filen inom den `<script></script>` taggar:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
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

När en användare klickar på **logga In** knappen för första gången den `signIn` metodanrop `loginPopup` att logga in användaren. Den här metoden innebär att öppna ett popup-fönster med de *Microsoft identity-plattformen endpoint* att fråga efter och verifiera användarens autentiseringsuppgifter. Till följd av en lyckad inloggning, användaren omdirigeras tillbaka till ursprungligt *index.html* sidan och en token tas emot, bearbetas av `msal.js` och den information som finns i token cachelagras. Denna token kallas den *ID-token* och innehåller grundläggande information om användare, till exempel användarens visningsnamn. Om du planerar att använda några data som tillhandahålls av denna token ändamål, måste du kontrollera att denna token ska valideras av backend-servern för att garantera att token utfärdats till en giltig användare för ditt program.

SPA som genereras av det här hjälper anrop `acquireTokenSilent` och/eller `acquireTokenPopup` att hämta en *åtkomsttoken* används för att fråga Microsoft Graph API för profilinformation för användaren. Om du behöver ett exempel som validerar ID-token kan ta en titt på [detta](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 exempel") exempelprogrammet i GitHub – exemplet använder en ASP .NET webb-API för tokenvalidering.

#### <a name="getting-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Efter den första inloggningen, du inte vill be användare att autentiseras på nytt varje gång som de behöver för att begära en token till en resurs – så *acquireTokenSilent* bör användas för de flesta fall för att hämta token. Det finns dock situationer när måste du tvinga användare att interagera med Microsoft identity-plattformen endpoint – några exempel är:

- Användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- Din app begär åtkomst till en resurs som användaren behöver ge sitt medgivande för
- Tvåfaktorsautentisering krävs

Anropa den *acquireTokenPopup* resulterar i ett popup-fönster (eller *acquireTokenRedirect* resulterar i att omdirigera användare till Microsoft identity-plattformen slutpunkten) när användare behöver interagera med någon Bekräfta sina autentiseringsuppgifter, ge samtycke till den begärda resursen eller du har slutfört tvåfaktorautentisering.

#### <a name="getting-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar hämtning och förnyelse av token utan någon användarinteraktion. Efter `loginPopup` (eller `loginRedirect`) körs för första gången `acquireTokenSilent` är den metod som ofta används för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop - eftersom anrop till begära eller förnya token görs tyst.
`acquireTokenSilent` kanske inte kan utföras i vissa fall – till exempel användarens lösenord har upphört att gälla. Programmet kan hantera det här undantaget på två sätt:

1. Göra ett anrop till `acquireTokenPopup` direkt, vilket innebär att uppmanar användaren att logga in. Det här mönstret används ofta i online-program där det finns inget oautentiserade innehåll i programmet tillgängligt för användaren. I exemplet som genererats av den här guidade konfigurationen används det här mönstret.

2. Program kan också göra en visuell indikering för användaren som en interaktiv inloggning krävs, så att användaren kan välja rätt tid att logga in eller programmet kan försöka `acquireTokenSilent` vid ett senare tillfälle. Detta används vanligtvis när användaren kan använda andra funktioner i programmet utan störs – till exempel finns oautentiserade innehåll i programmet. I det här fallet kan användaren avgöra när de vill logga in till den skyddade resursen eller uppdatera gammal information.

> [!NOTE]
> Den här snabbstarten används de `loginRedirect` och `acquireTokenRedirect` metoder när webbläsaren som används är Internet Explorer på grund av en [känt problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) rör hantering av popup-fönster genom att webbläsaren Internet Explorer.
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
