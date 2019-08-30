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
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135699"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Program med en sida – hämta en token för att anropa ett API

Mönstret för att hämta token för API: er med MSAL. js är att först försöka utföra en begäran om tyst `acquireTokenSilent` token med hjälp av metoden. När den här metoden anropas kontrollerar biblioteket först cacheminnet i webbläsarens lagrings utrymme för att se om det finns en giltig token och returnerar den. När det inte finns någon giltig token i cacheminnet, skickar den en begäran om tyst token till Azure Active Directory (Azure AD) från en dold iframe. Den här metoden gör det också möjligt för biblioteket att förnya tokens. För ytterligare information om enkel inloggning och token för token i Azure AD, se [livs längd](active-directory-configurable-token-lifetimes.md)för token.

Förfrågningar om obevakade token till Azure AD kan Miss lyckas av vissa orsaker, till exempel en utgången Azure AD-session eller en lösen ords ändring. I så fall kan du anropa en av de interaktiva metoderna (vilket uppmanas användaren) att hämta tokens.

* [Hämta token med ett popup-fönster](#acquire-token-with-a-pop-up-window) med`acquireTokenPopup`
* [Hämta token med omdirigering](#acquire-token-with-redirect) med`acquireTokenRedirect`

**Välja mellan en pop-up-eller Redirect-upplevelse**

 Du kan inte använda en kombination av både popup-och omdirigerings metoder i ditt program. Valet mellan en popup-eller omdirigerings upplevelse beror på ditt program flöde.

* Om du inte vill att användaren ska navigera bort från huvud program sidan under autentiseringen rekommenderar vi att du använder popup-metoderna. Eftersom autentiseringen av autentisering sker i ett popup-fönster bevaras huvud programmets tillstånd.

* Det finns vissa fall där du kan behöva använda omdirigerings metoderna. Om användare av ditt program har webb läsar begränsningar eller principer där popup-fönster har inaktiverats, kan du använda metoderna för omdirigering. Vi rekommenderar också att du använder metoderna för omdirigering i Internet Explorer eftersom det finns vissa [kända problem med Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) vid hantering av popup-fönster.

Du kan ställa in de API-omfattningar som du vill att åtkomsttoken ska inkludera när du skapar begäran om åtkomst-token. Observera att alla begärda omfattningar inte kan beviljas i åtkomsttoken och är beroende av användarens medgivande.

## <a name="acquire-token-with-a-pop-up-window"></a>Hämta token med ett popup-fönster

### <a name="javascript"></a>JavaScript

Ovanstående mönster använder metoderna för en popup-upplevelse:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
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

MSAL vinkel omslutningen ger dig möjlighet att lägga till HTTP-spärren, som automatiskt får åtkomst-token tyst och kopplar dem till HTTP-begäranden till API: er.

Du kan ange omfång för API: er i `protectedResourceMap` alternativet config, som MsalInterceptor kommer att begära när de hämtar token automatiskt.

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

För att lyckas och Miss lyckas med hämtningen av tysta token ger MSAL-vinkeln återanrop som du kan prenumerera på. Det är också viktigt att komma ihåg att avbryta prenumerationen.

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

Du kan också uttryckligen Hämta token med hjälp av metoderna för att hämta token enligt beskrivningen i biblioteket Core MSAL. js.

## <a name="acquire-token-with-redirect"></a>Hämta token med omdirigering

### <a name="javascript"></a>JavaScript

Mönstret är det som beskrivs ovan men som visas med en omdirigerings metod för att hämta token interaktivt. Du måste registrera återdirigerings återanropet som anges ovan.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-for-optional-claims"></a>Begäran om valfria anspråk
Du kan begära valfria anspråk i din app för att ange vilka ytterligare anspråk som ska ingå i tokens för ditt program. För att begära valfria anspråk i id_token kan du skicka ett stringified-anspråks objekt till claimsRequest-fältet i klassen AuthenticationParameters. TS.

Du kan använda valfria anspråk för följande ändamål:

- Inkludera ytterligare anspråk i token för ditt program.
- Ändra beteendet för vissa anspråk som Azure AD returnerar i tokens.
- Lägg till och få till gång till anpassade anspråk för ditt program.


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
Om du vill veta mer om valfria anspråk kan du skriva ut [valfria anspråk](active-directory-optional-claims.md)


### <a name="angular"></a>Angular

Detta är samma som beskrivs ovan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropar ett webb-API](scenario-spa-call-api.md)
