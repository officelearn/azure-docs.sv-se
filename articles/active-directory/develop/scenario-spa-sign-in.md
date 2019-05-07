---
title: Ensidesapplikation (logga in) - Microsoft identity-plattformen
description: Lär dig hur du skapar ett enkelsidigt program (logga in)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138792"
---
# <a name="single-page-application---sign-in"></a>Ensidesprogram - inloggning

Lär dig hur du lägger till inloggning till koden för din ensidesprogram.

Innan du kan hämta token för att få åtkomst till API: er i ditt program, måste en autentiserad användare-kontext. Du kan logga in användare i ditt program i MSAL.js på två sätt:

* [Logga in med ett popup-fönster](#sign-in-with-a-pop-up-window) med `loginPopup` metod
* [Logga in med omdirigering](#sign-in-with-redirect) med `loginRedirect` metod

Du kan också välja att skicka scope för API: erna som du behöver om användarens tillstånd vid tidpunkten för inloggningen.

> [!NOTE]
> Om programmet redan har åtkomst till en autentiserad användarkontext eller id token, kan du hoppa över steg för inloggning och direkt hämta token. Mer information finns i [sso utan msal.js inloggning](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Välja mellan en popup-fönstret eller omdirigerings-upplevelse

Du kan inte använda en kombination av både popup-fönstret och omdirigerings-metoderna i ditt program. Valet mellan en popup-fönstret eller omdirigerings-upplevelse beror på ditt flöde för programmet.

* Om du inte vill att användaren att navigera bort från sidan huvudprogrammet under autentiseringen, rekommenderar vi att du använder popup-metoder. Eftersom autentisering omdirigering sker i ett popup-fönster, bevaras tillståndet för det huvudsakliga programmet.

* Det finns vissa fall där du kan behöva använda omdirigerings-metoder. Om användare av ditt program har begränsningar för webbläsare eller principer där popup-fönster har inaktiverats kan använda du metoderna omdirigering. Använda omdirigerings-metoder med webbläsaren Internet Explorer eftersom det finns vissa [kända problem med Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) vid hantering av popup-fönster.

## <a name="sign-in-with-a-pop-up-window"></a>Logga in med ett popup-fönster

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL Angular omslutningen kan du skydda specifika vägar i ditt program genom att bara lägga till den `MsalGuard` till route-definition. Den här guard anropar metoden för att logga in när den vägen används.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

En popup-fönstret upplevelse, aktivera den `popUp` config-alternativet. Du kan även skicka scope som kräver godkännande på följande sätt:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Logga in med omdirigering

### <a name="javascript"></a>JavaScript

Omdirigerings-metoder returnerar inte ett löfte på grund av navigering bort från den huvudsakliga appen. För att bearbeta och få åtkomst till de returnerade token kommer du behöva registrera lyckas och fel återanrop innan du anropar omdirigerings-metoder.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Koden här är samma enligt beskrivningen ovan under logga in med ett avsnitt i popup-fönstret. Standard-flödet är omdirigering.

> [!NOTE]
> ID-token innehåller inte godkända scope och visar endast den autentiserade användaren. Godkända scope returneras i åtkomsttoken som du hämtar i nästa steg.

## <a name="sign-out"></a>Logga ut

MSAL-biblioteket innehåller en `logout` metod som rensar cachen i webbläsaren lagringen och skickar en utloggning begäran till Azure AD. Efter utloggning omdirigerar den tillbaka till startsidan för programmet som standard.

Du kan konfigurera den URI som ska den omdirigera efter logga ut genom att ange den `postLogoutRedirectUri`. URI: N bör också registreras som Logout URI i dina programregistrering.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en token som appen](scenario-spa-acquire-token.md)
