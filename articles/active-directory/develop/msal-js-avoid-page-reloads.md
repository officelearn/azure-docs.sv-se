---
title: Undvik att sidan läser in (Microsoft Authentication Library för JavaScript) | Azure
description: Lär dig hur du undviker sidan läser in när du hämtar och förnyande token tyst med Microsoft Authentication Library för JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420475"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Undvika sidan läser in när du hämtar och förnyande token tyst med MSAL.js
Microsoft Authentication Library för JavaScript (MSAL.js) använder dolda `iframe` element att skaffa och förnya token tyst i bakgrunden. Azure AD returnerar token tillbaka till den registrerade redirect_uri som anges i tokenbegäran (som standard är detta roten appsidan). Eftersom svaret är en 302, det resulterar i den HTML motsvarar den `redirect_uri` komma läses in i den `iframe`. Vanligtvis appens `redirect_uri` är sidan om rot och detta gör att den läses in på nytt.

I annat fall om navigera till roten appsida kräver autentisering, det kan leda till kapslade `iframe` element eller `X-Frame-Options: deny` fel.

Eftersom MSAL.js går inte att stänga 302 som utfärdats av Azure AD och krävs för att bearbeta den returnerade token, den kan inte förhindra den `redirect_uri` från komma läses in i den `iframe`.

Om du vill undvika hela appen läser in igen igen eller andra fel som orsakats på grund av detta, följer du dessa lösningar.

## <a name="specify-different-html-for-the-iframe"></a>Ange olika HTML för iframe

Ange den `redirect_uri` egenskapen config till en enkel sida som inte kräver autentisering. Du måste se till att den överensstämmer med den `redirect_uri` registrerad i Azure-portalen. Detta påverkar inte användarens inloggningsupplevelse som MSAL sparar startsidan när användaren börjar inloggningen och omdirigerar tillbaka till den exakta platsen när inloggningen har slutförts.

## <a name="initialization-in-your-main-app-file"></a>Initieringen i den huvudsakliga appfilen

Om din app är strukturerade, så att det finns en central Javascript-fil som definierar appens initieringen, Routning och annat, du villkorligt kan läsa in din app-moduler baserat på om appen läses in i en `iframe` eller inte. Exempel:

I AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

I Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Nästa steg
Läs mer om [att skapa en ensidesapplikation (SPA)](scenario-spa-overview.md) med MSAL.js.