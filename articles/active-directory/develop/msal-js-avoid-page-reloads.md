---
title: Undvik att hämta sidor (Microsoft Authentication Library för Java Script)
titleSuffix: Microsoft identity platform
description: Lär dig hur du undviker hämtning av sidor vid hämtning och förnyelse av tokens i bakgrunden med Microsoft Authentication Library för Java Script (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29edafdc27a3835653f82ec36d576a4871e66155
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803099"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Undvik hämtning av sidor vid hämtning och förnyelse av tokens tyst med MSAL. js
Microsoft Authentication Library för Java Script (MSAL. js) använder dolda `iframe`-element för att hämta och förnya token tyst i bakgrunden. Azure AD returnerar token tillbaka till den registrerade redirect_uri som anges i Tokenbegäran (som standard är appens rot sida). Eftersom svaret är en 302 resulterar det i HTML-koden som motsvarar `redirect_uri` som läses in i `iframe`. Vanligt vis är appens `redirect_uri` rot sidan och detta gör att den kan läsas in på nytt.

I andra fall kan det hända att om du navigerar till appens rot sida kräver autentisering, kan det leda till kapslade `iframe` element eller `X-Frame-Options: deny` fel.

Eftersom MSAL. js inte kan stänga av 302 som utfärdats av Azure AD och som krävs för att bearbeta den returnerade token, kan den inte hindra `redirect_uri` från att läsas in i `iframe`.

För att undvika att hela appen laddas om igen eller andra fel som orsakas av detta, följer du dessa lösningar.

## <a name="specify-different-html-for-the-iframe"></a>Ange en annan HTML för iframe

Ange egenskapen `redirect_uri` i konfig till en enkel sida som inte kräver autentisering. Du måste kontrol lera att den matchar `redirect_uri` som är registrerad i Azure Portal. Detta påverkar inte användarens inloggnings upplevelse eftersom MSAL sparar start sidan när användaren påbörjar inloggnings processen och dirigerar tillbaka till den exakta platsen när inloggningen har slutförts.

## <a name="initialization-in-your-main-app-file"></a>Initiera i huvud program filen

Om din app är strukturerad så att det finns en central JavaScript-fil som definierar appens initiering, Routning och andra saker kan du villkorligt läsa in dina app-moduler baserat på om appen läses in i en `iframe` eller inte. Exempel:

I AngularJS: app. js

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

I vinkel: app. module. TS

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
Lär dig mer om att [skapa ett enda webb program (Spa)](scenario-spa-overview.md) med MSAL. js.