---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 94d57abc95dabf1da579f6d2105ca6c74140a86f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293651"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Använd Microsoft Authentication Library (MSAL) för att logga in användaren

1.  Skapa en fil som heter `app.js`. Om du använder Visual Studio väljer du projektet (rotmappen för projektet), högerklicka och välj: `Add`  >  `New Item`  >  `JavaScript File`:
2.  Lägg till följande kod till din `app.js` fil:

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show sign-out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });
    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from sign-in
 * @param {object} error - The error string
 * @param {string} tokenType - The token type: For loginRedirect, tokenType = "id_token". For acquireTokenRedirect, tokenType:"access_token".
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>Mer information

När en användare klickar på *”anropa Microsoft Graph API-* knappen för första gången `callGraphApi` metodanrop `loginRedirect` att logga in användaren. Den här metoden innebär att omdirigera användaren till den *Microsoft Azure Active Directory v2-slutpunkten* att fråga efter och verifiera användarens autentiseringsuppgifter. Till följd av en lyckad inloggning, användaren omdirigeras tillbaka till ursprungligt *index.html* sidan och en token tas emot, bearbetas av `msal.js` och den information som finns i token cachelagras. Denna token kallas den *ID-token* och innehåller grundläggande information om användare, till exempel användarens visningsnamn. Om du planerar att använda några data som tillhandahålls av denna token ändamål, måste du kontrollera att denna token ska valideras av backend-servern för att garantera att token utfärdats till en giltig användare för ditt program.

SPA som genererats av den här guiden gör inte använda direkt av ID-token – i stället anropas `acquireTokenSilent` och/eller `acquireTokenRedirect` att hämta en *åtkomsttoken* används för att fråga Microsoft Graph API. Om du behöver ett exempel som validerar ID-token kan ta en titt på [detta](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 exempel") exempelprogrammet i GitHub – exemplet använder en ASP .NET webb-API för tokenvalidering.

#### <a name="getting-a-user-token-interactively"></a>Hämta en användaren token interaktivt

Efter den första inloggningen, du inte vill be användare att autentiseras på nytt varje gång som de behöver för att begära en token till en resurs – så *acquireTokenSilent* bör användas för de flesta fall för att hämta token. Det finns dock situationer när måste du tvinga användare att interagera med Azure Active Directory v2-slutpunkten – några exempel är:
- Användare kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- Ditt program begär åtkomst till en resurs som användaren behöver för att godkänna
- Tvåstegs-autentisering krävs

Anropa den *acquireTokenRedirect(scope)* leda till att omdirigera användare till Azure Active Directory v2-slutpunkten (eller *acquireTokenPopup(scope)* resultat på ett popup-fönster) när användare behöver interagera genom att antingen bekräfta sina autentiseringsuppgifter, ge samtycke till den begärda resursen eller du har slutfört factor de två-autentisering.

#### <a name="getting-a-user-token-silently"></a>Hämta en användaren token tyst
Den ` acquireTokenSilent` metoden hanterar token anskaffning och förnyelse utan någon användarinteraktion. Efter `loginRedirect` (eller `loginPopup`) körs för första gången `acquireTokenSilent` är den metod som ofta används för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop - eftersom anrop till begära eller förnya token görs tyst.
`acquireTokenSilent` kanske inte kan utföras i vissa fall – till exempel användarens lösenord har upphört att gälla. Programmet kan hantera det här undantaget på två sätt:

1.  Göra ett anrop till `acquireTokenRedirect` direkt, vilket innebär att uppmanar användaren att logga in. Det här mönstret används ofta i online-program där det finns inget oautentiserade innehåll i programmet tillgängligt för användaren. I exemplet som genererats av den här guidade konfigurationen används det här mönstret.

2. Program kan också göra en visuell indikering för användaren som en interaktiv inloggning krävs, så att användaren kan välja rätt tid att logga in eller programmet kan försöka `acquireTokenSilent` vid ett senare tillfälle. Detta används vanligtvis när användaren kan använda andra funktioner i programmet utan störs – till exempel finns oautentiserade innehåll i programmet. I det här fallet kan användaren avgöra när de vill logga in till den skyddade resursen eller uppdatera gammal information.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph API med hjälp av den token som du precis fick

Lägg till följande kod till din `app.js` fil:

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddade API

I det exempelprogram som skapats av den här guiden, den `callWebApiWithToken()` metod för att göra HTTP `GET` begäran mot en skyddad resurs som kräver ett token och återgå sedan innehållet till anroparen. Den här metoden lägger till förvärvade token i den *HTTP auktoriseringsrubrik*. För exempelprogrammet som skapats av den här guiden, resursen är Microsoft Graph API *mig* slutpunkt – som visar användarens profilinformation.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

Lägg till följande kod till din `app.js` fil:

```javascript
/**
 * Sign out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
