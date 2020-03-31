---
title: Inloggning för en sida & ut logga ut – Microsofts identitetsplattform | Azure
description: Lär dig hur du skapar ett ensidigt program (inloggning)
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
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159972"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Enkelsidesprogram: Logga in och logga ut

Läs om hur du lägger till inloggning i koden för ditt ensidiga program.

Innan du kan få token för att komma åt API:er i ditt program behöver du en autentiserat användarkontext. Du kan logga in användare på ditt program i MSAL.js på två sätt:

* [Popup-fönster](#sign-in-with-a-pop-up-window)med hjälp `loginPopup` av metoden
* [Omdirigering](#sign-in-with-redirect), med hjälp av `loginRedirect` metoden

Du kan också skicka scope för API:er som du behöver användaren för att godkänna vid tidpunkten för inloggningen.

> [!NOTE]
> Om ditt program redan har åtkomst till en autentiserat användarkontext eller ID-token kan du hoppa över inloggningssteget och direkt hämta token. Mer information finns i [SSO utan MSAL.js-inloggning](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Välja mellan en popup- eller omdirigeringsupplevelse

Du kan inte använda både popup- och omdirigeringsmetoderna i programmet. Valet mellan en popup- eller omdirigeringsupplevelse beror på ditt programflöde:

* Om du inte vill att användarna ska flytta bort från huvudprogramsidan under autentiseringen rekommenderar vi popup-metoden. Eftersom autentiseringsomdirigeringen sker i ett popup-fönster bevaras huvudprogrammets tillstånd.

* Om användarna har webbläsarbegränsningar eller principer där popup-fönster är inaktiverade kan du använda omdirigeringsmetoden. Använd omdirigeringsmetoden med webbläsaren Internet Explorer, eftersom det finns [kända problem med popup-fönster i Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Logga in med ett popup-fönster

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

Med MSAL-vinkelomslaget kan du skydda specifika vägar i `MsalGuard` programmet genom att lägga till flödesdefinitionen. Den här vakten anropar metoden för att logga in när rutten nås.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Aktivera konfigurationsalternativet `popUp` för ett popup-fönster. Du kan också skicka de scope som kräver samtycke enligt följande:

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
---

## <a name="sign-in-with-redirect"></a>Logga in med omdirigering

# <a name="javascript"></a>[Javascript](#tab/javascript)

Omdirigeringsmetoderna returnerar inte ett löfte på grund av flytten bort från huvudappen. Om du vill bearbeta och komma åt returnerade token måste du registrera lyckade och felåteruppringningar innan du anropar omdirigeringsmetoderna.

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

# <a name="angular"></a>[Angular](#tab/angular)

Koden här är densamma som beskrivs tidigare i avsnittet om inloggning med ett popup-fönster. Standardflödet omdirigeras.

> [!NOTE]
> ID-token innehåller inte de medgivande scope och representerar endast den autentiserade användaren. De medgivande scope returneras i åtkomsttoken, som du får i nästa steg.

---

## <a name="sign-out"></a>Logga ut

MSAL-biblioteket innehåller `logout` en metod som rensar cacheminnet i webbläsarens lagring och skickar en utskrivningsbegäran till Azure Active Directory (Azure AD). När du har loggat ut omdirigeras biblioteket tillbaka till programmets startsida som standard.

Du kan konfigurera den URI som den ska `postLogoutRedirectUri`omdirigera efter ut logga ut genom att ange . Denna URI bör också registreras som utloggning URI i din ansökan registrering.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

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

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta en token för appen](scenario-spa-acquire-token.md)
