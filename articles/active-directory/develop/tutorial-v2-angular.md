---
title: Ensidig självstudiekurs för ensidig app - Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur Angular SPA-program kan anropa ett API som kräver åtkomsttoken från slutpunkten för Microsoft-identitetsplattform
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 63eda0c5d7b5ef4741e8244fbde290d13b54c5fb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880847"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application-spa"></a>Logga in användare och anropa Microsoft Graph API från ett vinkellikt ensidigt program (SPA)

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

Den här guiden visar hur ett vinkellikt ensidigt program (SPA) kan:
- Logga in personliga konton samt arbets- och skolkonton
- Hämta en åtkomsttoken
- Anropa Microsoft Graph API eller andra API:er som kräver åtkomsttoken från slutpunkten för *Microsoft-identitetsplattformen*

>[!NOTE]
>Denna handledning kommer att gå igenom hur du skapar en ny Angular SPA med MSAL. Om du vill ladda ner en exempelapp läser du [snabbstarten](quickstart-v2-angular.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelappen som genereras av den här guiden

![Visar hur exempelappen som genereras av den här självstudien fungerar](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Exempelprogrammet som skapas av den här guiden gör det möjligt för ett vinkelspalett att fråga Microsoft Graph API eller ett webb-API som accepterar token från slutpunkten för Microsoft-identitetsplattformen. MSAL för vinkelbiblioteket är ett omslag till msal.js-biblioteket. Det gör det möjligt för Angular (6+) program att autentisera företagsanvändare med Microsoft Azure Active Directory (AAD), Microsoft-kontoanvändare (MSA), användare av social identitet (Facebook, Google, LinkedIn, etc.) och få tillgång till Microsoft Cloud eller Microsoft Graph. I det här fallet när en användare loggar in, begärs en åtkomsttoken och läggs till HTTP-begäranden via auktoriseringshuvudet. Tokenförvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft-autentiseringsbibliotek för JavaScript-vinkelomslag|

> [!NOTE]
> *Msal.js* riktar in sig på slutpunkten för Microsoft identity platform, som gör det möjligt för personliga konton och skol- och arbetskonton att logga in och hämta token. Slutpunkten för Microsoft-identitetsplattformen har [vissa begränsningar](../azuread-dev/azure-ad-endpoint-comparison.md#limitations).
> För att förstå skillnaderna mellan v1.0- och v2.0-slutpunkterna finns i [jämförelseguiden för slutpunkt](../azuread-dev/azure-ad-endpoint-comparison.md).

<!--end-collapse-->


## <a name="prerequisites"></a>Krav

* Om du vill köra den här självstudien behöver du en lokal webbserver, till exempel [Node.js](https://nodejs.org/en/download/)

* Installera en integrerad utvecklingsmiljö (IDE), till exempel [Visual Studio Code,](https://code.visualstudio.com/download)för att redigera projektfilerna.

* Instruktionerna i den här guiden är baserade på Node.js

## <a name="create-your-project"></a>Skapa ditt projekt

Generera ett nytt vinkelprogram med följande npm-kommandon:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a the home, profile page)
```

## <a name="register-your-application"></a>Registrera ditt program

Följ instruktionerna för att registrera ett ensidigt program i [Azure-portalen.](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration)

 På sidan App **översikt** av din registrering, notera **application (klient) ID-värdet** för senare användning.

 Registrera din Redirect `http://localhost:4200/` **URI** som och aktivera implicita bidragsinställningar.

#### <a name="configure-your-angular-application"></a>Konfigurera det kantiga programmet

1. I *src/app-mappen* *redigerar du app.module.ts* och lägger `MSALModule` till såväl `imports` i som `isIE` const som visas nedan:

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant id
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // set to true for IE 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Ersätt dessa värden som sådana:

    |Värdenamn|Om|
    |---------|---------|
    |Enter_the_Application_Id_Here|På sidan **Översikt för** din ansökan registrering, detta är din **Ansökan (klient) ID** |
    |Enter_the_Cloud_Instance_Id_Here|Detta är instansen av Azure-molnet. För huvud- eller globala https://login.microsoftonline.comAzure-molnet anger du . För nationella moln (till exempel Kina), se [Nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
    |Enter_the_Tenant_Info_Here| Ange ett av följande alternativ: 1) Om programmet stöder *konton i den här organisationskatalogen*ersätter du det här värdet med **katalog-ID:t (klient)** eller **klientnamn** (till exempel *contoso.microsoft.com*). 2) Om ditt program stöder *konton i en organisationskatalog* **ersätter**du det här värdet med organisationer . 3) Om ditt program stöder *konton i en organisationskatalog och personliga Microsoft-konton*ersätter du det här värdet med **vanliga**. 4) Om du bara vill begränsa supporten till *personliga Microsoft-konton*ersätter du det här värdet med **konsumenter**. |
    |Enter_the_Redirect_Uri_Here|Ersätt med`http://localhost:4200`|

    Mer information om tillgängliga konfigurerbara alternativ finns i [Initiera klientprogram](msal-js-initializing-client-applications.md).

2. Lägg till följande import överst i filen i samma fil:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

    ### <a name="import-modules"></a>Importera moduler
    Lägg till följande importsatser högst upp i`src/app/app.component.ts`
    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
    ## <a name="sign-in-a-user"></a>Logga in en användare

    Lägg till följande `AppComponent` kod för att logga in en användare:

    ```javascript
    export class AppComponent implements OnInit {
        constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

        login() {
            const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

            if (isIE) {
              this.authService.loginRedirect({
                extraScopesToConsent: ["user.read", "openid", "profile"]
              });
            } else {
              this.authService.loginPopup({
                extraScopesToConsent: ["user.read", "openid", "profile"]
              });
            }
        }
    }
    ```

> [!TIP]
> Vi rekommenderar `loginRedirect` att du använder för Internet Explorer-användare.

## <a name="acquire-a-token"></a>Hämta en token

### <a name="angular-interceptor"></a>Vinkel interceptor

MSAL Angular `Interceptor` tillhandahåller en klass som automatiskt hämtar token för utgående begäranden som använder `http` vinkelklienten till kända skyddade resurser.

Inkludera först `Interceptor` klassen som leverantör till ditt program:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Ange sedan en karta över `MsalModule.forRoot()` skyddade `protectedResourceMap` resurser för att `consentScopes`som och inkludera dessa scope i:

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // set to true for IE 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Hämta slutligen en användares profil med en HTTP-begäran.

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>förvärvTokenSilent, förvärvaTokenPopup, förvärvaTokenRedirect
MSAL använder tre metoder för `acquireTokenRedirect` `acquireTokenPopup`att `acquireTokenSilent`hämta token: , och . Vi rekommenderar dock att du använder Interceptor istället för vinkelappar, som visas i föregående avsnitt.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar tokenförvärv och förnyelse utan användarinteraktion. När `loginRedirect` metoden `loginPopup` eller körs för första `acquireTokenSilent` gången används ofta för att hämta token som används för att komma åt skyddade resurser i efterföljande anrop. Anrop för att begära eller förnya token görs tyst.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

Var `scopes` innehåller scope som begärs att returneras i åtkomsttoken för API:et.

Ett exempel:

* `["user.read"]`för Microsoft Graph
* `["<Application ID URL>/scope"]`för anpassade webb-API:er (det vill ha) `api://<Application ID>/access_as_user`

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Ibland behöver du användaren för att interagera med slutpunkten för Microsoft identity platform. Ett exempel:

* Användare kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla.
* Ditt program begär åtkomst till ytterligare resursomfattningar som användaren behöver samtycka till.
* Tvåfaktorsautentisering krävs.

Det rekommenderade mönstret för `acquireTokenSilent` de flesta program är att `acquireTokenPopup` ringa `acquireTokenRedirect`först, sedan fånga undantaget och sedan ringa (eller) för att starta en interaktiv begäran.

Att `acquireTokenPopup` ringa resulterar i ett popup-inloggningsfönster. Du kan `acquireTokenRedirect` också omdirigera användare till slutpunkten för Microsoft identity platform. I det fönstret måste användarna bekräfta sina autentiseringsuppgifter, ge medgivande till den nödvändiga resursen eller slutföra tvåfaktorsautentisering.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Den här snabbstarten använder metoderna `loginRedirect` och `acquireTokenRedirect` med Microsoft Internet Explorer på grund av ett känt [problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som rör hantering av popup-fönster av Internet Explorer.

## <a name="log-out"></a>Logga ut

Lägg till följande kod för att logga ut en användare.

```javascript
logout() {
  this.authService.logout();
}
```

#### <a name="add-ui"></a>Lägga till användargränssnitt
Kassan [exempelprogrammet](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) för ett enkelt exempel på hur du lägger till användargränssnittet med komponentbiblioteket Vinkelmaterial.

## <a name="test-your-code"></a>Testa koden

1.  Starta webbservern för att lyssna på porten genom att köra följande kommandon vid en kommandoradsfråga från programmappen:

    ```bash
    npm install
    npm start
    ```
1. I webbläsaren anger **http://localhost:4200** **http://localhost:{port}** eller , där *porten* är den port som webbservern lyssnar på.


### <a name="provide-consent-for-application-access"></a>Ge samtycke till tillgång till program

Första gången du loggar in på ditt program uppmanas du att ge den åtkomst till din profil och logga in dig:

![Fönstret "Begärda behörigheter"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)



<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver *att användaren.läser* omfång för att läsa en användares profil. Som standard läggs det här scopet automatiskt till i alla program som är registrerade på registreringsportalen. Andra API:er för Microsoft Graph, samt anpassade API:er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver till exempel *scopet Calendars.Read* för att kunna visa användarens kalendrar.

Om du vill komma åt användarens kalendrar i ett program lägger du till den delegerade behörigheten *Calendars.Read* i programregistreringsinformationen. Lägg sedan till *calendars.Read-scopet* i anropet. `acquireTokenSilent`

>[!NOTE]
>Användaren kan uppmanas att ange ytterligare medgivanden när du ökar antalet scope.

Om ett backend-API inte kräver ett scope (rekommenderas inte) kan du använda *clientId* som scope i anropen för att hämta token.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Bläddra i MSAL-osien för dokumentation, vanliga frågor, problem med mera:

> [!div class="nextstepaction"]
> [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)
