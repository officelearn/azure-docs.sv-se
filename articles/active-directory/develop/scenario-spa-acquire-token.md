---
title: Skaffa en token för att anropa ett webb-API (ensidiga appar) – Microsoft identity platform | Azure
description: Lär dig hur du skapar ett ensidigt program (hämta en token för att anropa ett API)
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: eeba01a609a1a21ed564c0b9cb78a28a4ad5c95a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882326"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Ensidigt program: Skaffa en token för att anropa ett API

Mönstret för att hämta token för API:er med MSAL.js `acquireTokenSilent` är att först försöka sig på en tyst tokenbegäran med hjälp av metoden. När den här metoden anropas kontrollerar biblioteket först cacheminnet i webbläsarens lagring för att se om det finns en giltig token och returnerar den. När ingen giltig token finns i cacheminnet skickas en tyst tokenbegäran till Azure Active Directory (Azure AD) från en dold iframe. Med den här metoden kan biblioteket också förnya token. Mer information om enstaka inloggningssessions- och tokenlivstidsvärden i Azure AD finns i [Token lifetimes](active-directory-configurable-token-lifetimes.md).

De tysta tokenbegärandena till Azure AD kan misslyckas av orsaker som en utgången Azure AD-session eller en lösenordsändring. I så fall kan du anropa en av de interaktiva metoderna (som kommer att uppmana användaren) att hämta token:

* [Popup-fönster](#acquire-a-token-with-a-pop-up-window), med hjälp av`acquireTokenPopup`
* [Omdirigering](#acquire-a-token-with-a-redirect), genom att använda`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Välj mellan en popup- eller omdirigeringsupplevelse

 Du kan inte använda både popup- och omdirigeringsmetoderna i programmet. Valet mellan en popup- eller omdirigeringsupplevelse beror på ditt programflöde:

* Om du inte vill att användarna ska flytta bort från huvudprogramsidan under autentiseringen rekommenderar vi popup-metoden. Eftersom autentiseringsomdirigeringen sker i ett popup-fönster bevaras huvudprogrammets tillstånd.

* Om användare har webbläsarbegränsningar eller principer där popup-fönster är inaktiverade kan du använda omdirigeringsmetoden. Använd omdirigeringsmetoden med webbläsaren Internet Explorer, eftersom det finns [kända problem med popup-fönster i Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Du kan ange de API-scope som du vill att åtkomsttoken ska inkludera när den skapar åtkomsttokenbegäran. Observera att alla begärda scope kanske inte beviljas i åtkomsttoken. Det beror på användarens samtycke.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Skaffa en token med ett popup-fönster

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Följande kod kombinerar det tidigare beskrivna mönstret med metoderna för en popup-upplevelse:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL-vinkelomslaget tillhandahåller HTTP-interceptor, som automatiskt hämtar åtkomsttoken tyst och bifogar dem till HTTP-begäranden till API:er.

Du kan ange scope för API:er i konfigurationsalternativet. `protectedResourceMap` `MsalInterceptor`kommer att begära dessa scope när token automatiskt hämtas.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

För att lyckas och misslyckas med det tysta tokeninhämtningen tillhandahåller MSAL Angular motringningar som du kan prenumerera på. Det är också viktigt att komma ihåg att avsluta prenumerationen.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternativt kan du uttryckligen hämta token med hjälp av metoderna acquire-token som beskrivs i det centrala MSAL.js-biblioteket.

---

## <a name="acquire-a-token-with-a-redirect"></a>Skaffa en token med en omdirigering

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Följande mönster beskrivs som beskrivits tidigare men visas med en omdirigeringsmetod för att hämta token interaktivt. Du måste registrera den omdirigerade motringningen som tidigare nämnts.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Begär valfria anspråk

Du kan använda valfria anspråk för följande ändamål:

- Inkludera ytterligare anspråk i token för ditt program.
- Ändra beteendet för vissa anspråk som Azure AD returnerar i token.
- Lägg till och komma åt anpassade anspråk för ditt program.

Om du vill `IdToken`begära valfria anspråk i kan `claimsRequest` du `AuthenticationParameters.ts` skicka ett stringifierat anspråksobjekt till fältet för klassen.

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

Mer information finns i [Valfria anspråk](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Den här koden är densamma som beskrivits tidigare.

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa ett webb-API](scenario-spa-call-api.md)
