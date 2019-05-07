---
title: Ensidesapplikation (hämta en token för att anropa ett API) - Microsoft identity-plattformen
description: Lär dig hur du skapar ett enkelsidigt program (hämta en token för att anropa ett API)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138820"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Ensidesprogram – hämta en token för att anropa ett API

Mönstret för att hämta token för API: er med MSAL.js är att först försöka en tyst tokenbegäran med hjälp av den `acquireTokenSilent` metoden. När den här metoden anropas kontrollerar cachen i webbläsaren-lagring för att se om en giltig token finns och returnerar det först i biblioteket. När det finns ingen giltig token i cacheminnet, skickar en begäran om tyst token till Azure Active Directory (Azure AD) från en dold iframe. Den här metoden kan också biblioteket för att förnya token. Mer information om enkel inloggning, session och livslängd för token-värden i Azure AD finns i [token livslängd](active-directory-configurable-token-lifetimes.md).

Tyst token-förfrågningar till Azure AD kan inte utföras i vissa orsaker, exempelvis en har upphört att gälla Azure AD-session eller en lösenordsändring. Du kan i så fall kan anropa en av de interaktiva metoder (som uppmanar användaren) för att hämta token.

* [Hämta token med ett popup-fönster](#acquire-token-with-a-pop-up-window) med hjälp av `acquireTokenPopup`
* [Hämta token med omdirigering](#acquire-token-with-redirect) med hjälp av `acquireTokenRedirect`

**Välja mellan en popup-fönstret eller omdirigerings-upplevelse**

 Du kan inte använda en kombination av både popup-fönstret och omdirigerings-metoderna i ditt program. Valet mellan en popup-fönstret eller omdirigerings-upplevelse beror på ditt flöde för programmet.

* Om du inte vill att användaren att navigera bort från sidan huvudprogrammet under autentiseringen, rekommenderar vi att du använder popup-metoder. Eftersom autentisering omdirigering sker i ett popup-fönster, bevaras tillståndet för det huvudsakliga programmet.

* Det finns vissa fall där du kan behöva använda omdirigerings-metoder. Om användare av ditt program har begränsningar för webbläsare eller principer där popup-fönster har inaktiverats kan använda du metoderna omdirigering. Vi rekommenderar också för att använda omdirigerings-metoder med webbläsaren Internet Explorer, eftersom det finns vissa [kända problem med Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) vid hantering av popup-fönster.

Du kan ställa in API-omfång som du vill att den åtkomst-token för att inkludera när du skapar begäran om åtkomsttoken. Observera att alla begärda omfång beviljas inte i åtkomst-token och beror på användarens medgivande.

## <a name="acquire-token-with-a-pop-up-window"></a>Hämta token med ett popup-fönster

### <a name="javascript"></a>JavaScript

Ovanstående mönstret med hjälp av metoder för en popup-upplevelse:

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

MSAL Angular omslutningen underlättar för att lägga till HTTP-standardstack `MsalInterceptor` som automatiskt hämta åtkomsttoken tyst och koppla dem till HTTP-begäranden till API: er.

Du kan ange omfång för API: er i den `protectedResourceMap` config-alternativ som begär MsalInterceptor när automatiskt hämta token.

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

För lyckade och misslyckade av tyst tokenförvärv ger MSAL Angular återanrop som du kan prenumerera på. Det är också viktigt att komma ihåg att avbryta prenumerationen.

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

Alternativt kan du också uttryckligen hämta token med hämta token metoder som beskrivs i MSAL.js Kärnbibliotek.

## <a name="acquire-token-with-redirect"></a>Hämta token med omdirigering

### <a name="javascript"></a>JavaScript

Mönstret är som beskrivs ovan men visas med en omdirigerings-metod för att hämta token interaktivt. Observera att du måste registrera motringningen omdirigering som nämns ovan.

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

### <a name="angular"></a>Angular

Det här är densamma som beskrivs ovan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa ett webb-API](scenario-spa-call-api.md)
