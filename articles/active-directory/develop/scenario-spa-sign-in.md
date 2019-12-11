---
title: Inloggning på en enda sida – Microsoft Identity Platform | Azure
description: Lär dig hur du skapar ett program med en enda sida (inloggning)
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
ms.openlocfilehash: 5d2494a7ad89c9e9b3a525ddd04290a08eb3af58
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962431"
---
# <a name="single-page-application-sign-in"></a>Program med en sida: inloggning

Lär dig hur du lägger till inloggning till koden för ditt program på en sida.

Innan du kan hämta token för att få åtkomst till API: er i ditt program behöver du en autentiserad användar kontext. Du kan logga in användare i ditt program i MSAL. js på två sätt:

* [Popup-fönster](#sign-in-with-a-pop-up-window)med hjälp av metoden `loginPopup`
* [Omdirigera](#sign-in-with-redirect)med hjälp av metoden `loginRedirect`

Du kan också välja att skicka omfattningarna för de API: er som du vill att användaren ska godkänna vid tidpunkten för inloggning.

> [!NOTE]
> Om programmet redan har åtkomst till en autentiserad användar kontext eller ID-token kan du hoppa över inloggnings steget och hämta token direkt. Mer information finns i [SSO utan MSAL. js-inloggning](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Välja mellan en pop-up-eller Redirect-upplevelse

Du kan inte använda båda metoderna pop-up och Redirect i ditt program. Valet mellan en popup-eller omdirigerings upplevelse beror på ditt program flöde:

* Om du inte vill att användarna ska kunna flytta bort från huvud program sidan under autentiseringen rekommenderar vi popup-metoden. Eftersom omdirigeringen av autentisering sker i ett popup-fönster bevaras huvud programmets tillstånd.

* Om användarna har begränsningar i webbläsaren eller principer där popup-fönster har inaktiverats kan du använda metoden Redirect. Använd metoden Redirect med Internet Explorer-webbläsaren, eftersom det finns [kända problem med popup-fönster i Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Logga in med ett popup-fönster

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
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

Med MSAL-vinkel omslutningen kan du skydda specifika vägar i ditt program genom att lägga till `MsalGuard` i väg definitionen. Det här skydds metoden anropar metoden för att logga in när den vägen nås.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Aktivera konfigurations alternativet `popUp` för en popup-fönster upplevelse. Du kan också skicka de omfattningar som kräver medgivande enligt följande:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Logga in med omdirigering

### <a name="javascript"></a>JavaScript

Omdirigerings metoderna returnerar inte ett löfte på grund av att huvud-appen har flyttats bort. Om du vill bearbeta och komma åt de returnerade tokens måste du registrera lyckade och felfria återanrop innan du anropar omdirigerings metoderna.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Koden här är samma som beskrivs tidigare i avsnittet om att logga in med ett popup-fönster. Standard flödet är omdirigerat.

> [!NOTE]
> ID-token innehåller inte de vidarebefordrade omfattningarna och representerar bara den autentiserade användaren. De godkända omfattningarna returneras i åtkomsttoken, som du hämtar i nästa steg.

## <a name="sign-out"></a>Logga ut

MSAL-biblioteket innehåller en `logout` metod som rensar cacheminnet i webbläsarens lagring och skickar en inloggningsbegäran till Azure Active Directory (Azure AD). Efter utloggning omdirigerar biblioteket tillbaka till program start sidan som standard.

Du kan konfigurera den URI som den ska omdirigeras till efter utloggning genom att ange `postLogoutRedirectUri`. Denna URI bör också registreras som utloggnings-URI i program registreringen.

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
> [Hämtar en token för appen](scenario-spa-acquire-token.md)
