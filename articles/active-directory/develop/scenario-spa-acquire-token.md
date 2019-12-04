---
title: Program med en sida (Hämta en token för att anropa ett API) – Microsoft Identity Platform
description: Lär dig hur du skapar ett program med en enda sida (Hämta en token för att anropa ett API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f1140d00671a706ce8839a73023dfad64d4663
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764771"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Program med en sida: Hämta en token för att anropa ett API

Mönstret för att hämta token för API: er med MSAL. js är att först försöka utföra en begäran om tyst token med hjälp av metoden `acquireTokenSilent`. När den här metoden anropas kontrollerar biblioteket först cacheminnet i webbläsarens lagring för att se om det finns en giltig token och returnerar den. När det inte finns någon giltig token i cacheminnet, skickar den en begäran om tyst token till Azure Active Directory (Azure AD) från en dold iframe. Den här metoden gör det också möjligt för biblioteket att förnya tokens. För ytterligare information om enkel inloggning och token för token i Azure AD, se [livs längd för token](active-directory-configurable-token-lifetimes.md).

Begär Anden om obevakade token till Azure AD kan Miss Miss kan bero på att en Azure AD-session eller ett lösen ord har ändrats. I så fall kan du anropa en av de interaktiva metoderna (vilket uppmanas användaren) att hämta tokens:

* [Popup-fönster](#acquire-a-token-with-a-pop-up-window)med `acquireTokenPopup`
* [Omdirigera](#acquire-a-token-with-a-redirect)genom att använda `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Välj mellan en popup-eller omdirigerings upplevelse

 Du kan inte använda båda metoderna pop-up och Redirect i ditt program. Valet mellan en popup-eller omdirigerings upplevelse beror på ditt program flöde:

* Om du inte vill att användarna ska flyttas bort från huvud program sidan under autentiseringen rekommenderar vi popup-metoden. Eftersom omdirigeringen av autentisering sker i ett popup-fönster bevaras huvud programmets tillstånd.

* Om användarna har webb läsar begränsningar eller principer där popup-fönster är inaktiverade, kan du använda metoden Redirect. Använd metoden Redirect med Internet Explorer-webbläsaren, eftersom det finns [kända problem med popup-fönster i Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Du kan ställa in de API-omfattningar som du vill att åtkomsttoken ska inkludera när den skapar åtkomsttokenbegäran. Observera att alla begärda omfattningar kanske inte beviljas i åtkomsttoken. Detta beror på användarens medgivande.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Hämta en token med ett popup-fönster

### <a name="javascript"></a>JavaScript

Följande kod kombinerar det tidigare beskrivna mönstret med metoder för en popup-upplevelse:

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

### <a name="angular"></a>Angular

MSAL vinkel omslutningen ger HTTP-spärren, som automatiskt får åtkomst-token och kopplar dem till HTTP-begäranden till API: er.

Du kan ange omfång för API: er i `protectedResourceMap` konfigurations alternativet. `MsalInterceptor` kommer att begära dessa scope när de automatiskt hämtar token.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

För att lyckas och Miss lyckas med hämtningen av tyst token tillhandahåller MSAL-vinkel återanrop som du kan prenumerera på. Det är också viktigt att komma ihåg att avbryta prenumerationen.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternativt kan du uttryckligen Hämta token genom att använda metoderna för att hämta token enligt beskrivningen i biblioteket Core MSAL. js.

## <a name="acquire-a-token-with-a-redirect"></a>Hämta en token med en omdirigering

### <a name="javascript"></a>JavaScript

Följande mönster är som beskrivits tidigare men visas med en metod för omdirigering för att förvärva token interaktivt. Du måste registrera återdirigerings återanropet som tidigare nämnts.

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
Du kan använda valfria anspråk i följande syfte:

- Inkludera ytterligare anspråk i token för ditt program.
- Ändra beteendet för vissa anspråk som Azure AD returnerar i tokens.
- Lägg till och få till gång till anpassade anspråk för ditt program. 

Om du vill begära valfria anspråk i `IdToken`kan du skicka ett stringified-anspråks objekt till fältet `claimsRequest` i `AuthenticationParameters.ts`-klassen.

### <a name="javascript"></a>JavaScript
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
Mer information finns i [valfria anspråk](active-directory-optional-claims.md).


### <a name="angular"></a>Angular

Den här koden är samma som beskrivs ovan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropar ett webb-API](scenario-spa-call-api.md)
