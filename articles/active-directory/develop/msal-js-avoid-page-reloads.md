---
title: Undvik sidreloader (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du undviker sidreloader när du hämtar och förnyar token tyst med hjälp av Microsoft Authentication Library for JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084953"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Undvik sidladdningar när du hämtar och förnyar token tyst med MSAL.js
Microsoft Authentication Library for JavaScript (MSAL.js) använder dolda `iframe` element för att hämta och förnya token tyst i bakgrunden. Azure AD returnerar token tillbaka till den registrerade redirect_uri som anges i tokenbegäran (som standard är detta appens rotsida). Eftersom svaret är en 302, resulterar det `redirect_uri` i HTML `iframe`motsvarar att få laddas i . Vanligtvis appens `redirect_uri` är rotsidan och detta gör att den laddas om.

I andra fall, om det krävs autentisering för att navigera till `iframe` appens rotsida, kan det leda till kapslade element eller `X-Frame-Options: deny` fel.

Eftersom MSAL.js inte kan stänga 302 som utfärdats av Azure AD och krävs `redirect_uri` för att `iframe`bearbeta den returnerade token, kan den inte hindra från att läsas in i .

Följ dessa lösningar för att undvika att hela appen laddas om igen eller andra fel som orsakas på grund av detta.

## <a name="specify-different-html-for-the-iframe"></a>Ange olika HTML-html-kod för iframe

Ange `redirect_uri` egenskapen på config till en enkel sida, som inte kräver autentisering. Du måste se till att `redirect_uri` den matchar med den registrerade i Azure-portalen. Detta kommer inte att påverka användarens inloggningsupplevelse som MSAL sparar startsidan när användaren börjar inloggningsprocessen och omdirigerar tillbaka till den exakta platsen efter inloggningen är klar.

## <a name="initialization-in-your-main-app-file"></a>Initiering i huvudappfilen

Om din app är strukturerad så att det finns en central Javascript-fil som definierar appens initiering, routning och andra `iframe` saker, kan du villkorligt läsa in dina appmoduler baserat på om appen läses in i en eller inte. Ett exempel:

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

I Vinkel: app.module.ts

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
Läs mer om [att skapa ett ensidigt program (SPA)](scenario-spa-overview.md) med MSAL.js.