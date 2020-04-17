---
title: Ensidig självstudiekurs för ensidig app - Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur Angular SPA-program kan anropa ett API som kräver åtkomsttoken från slutpunkten för Microsoft-identitetsplattformen.
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
ms.openlocfilehash: 84d7be64e7157c35639fec19cb28fe787fe29b19
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533895"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Självstudiekurs: Logga in användare och anropa Microsoft Graph API från ett kantigt ensidigt program

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av den här funktionen kan ändras före allmän tillgänglighet (GA).

Den här självstudien visar hur ett vinkellikt ensidigt program (SPA) kan:
- Logga in personliga konton, arbetskonton eller skolkonton.
- Hämta en åtkomsttoken.
- Anropa Microsoft Graph API eller andra API:er som kräver åtkomsttoken från slutpunkten för *Microsoft-identitetsplattformen*.

>[!NOTE]
>Den här självstudien visar hur du skapar ett nytt vinkelspa med hjälp av Microsoft Authentication Library (MSAL). Om du vill hämta en exempelapp läser du [snabbstarten](quickstart-v2-angular.md).

## <a name="how-the-sample-app-works"></a>Så här fungerar exempelappen

![Diagram som visar hur exempelappen som genereras i den här självstudien fungerar](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Exempelprogrammet som skapas i den här självstudien gör det möjligt för ett vinkelspalett att fråga Microsoft Graph API eller ett webb-API som accepterar token från slutpunkten för Microsoft identity-plattformen. MSAL för vinkelbiblioteket är ett omslag till msal.js-biblioteket. Det gör det möjligt för Angular -program (6+) att autentisera företagsanvändare med hjälp av Microsoft Azure Active Directory, Microsoft-kontoanvändare och användare av social identitet (till exempel Facebook, Google och LinkedIn). Biblioteket gör det också möjligt för programmen att få tillgång till Microsofts molntjänster eller Microsoft Graph.

I det här fallet när en användare loggar in, begärs en åtkomsttoken och läggs till HTTP-begäranden via auktoriseringshuvudet. Tokenförvärv och förnyelse hanteras av MSAL.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

Den här självstudien använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft-autentiseringsbibliotek för JavaScript-vinkelomslag|

> [!NOTE]
> *Msal.js* riktar in sig på slutpunkten för Microsoft identity platform, som gör det möjligt för personliga konton, arbetskonton och skolkonton att logga in och hämta token. Slutpunkten för Microsoft-identitetsplattformen har [vissa begränsningar](../azuread-dev/azure-ad-endpoint-comparison.md#limitations).
> För att förstå skillnaderna mellan v1.0- och v2.0-slutpunkterna finns i [jämförelseguiden för slutpunkt](../azuread-dev/azure-ad-endpoint-comparison.md).

Du hittar källkoden för MSAL.js-biblioteket i [AzureAD/microsoft-authentication-library-for-js-databasen](https://github.com/AzureAD/microsoft-authentication-library-for-js) på GitHub.

<!--end-collapse-->


## <a name="prerequisites"></a>Krav

För att köra den här självstudien behöver du:

* En lokal webbserver, till exempel [Node.js](https://nodejs.org/en/download/). Instruktionerna i den här självstudien baseras på Node.js.
* En integrerad utvecklingsmiljö (IDE), till exempel [Visual Studio Code,](https://code.visualstudio.com/download)för att redigera projektfilerna.

## <a name="create-your-project"></a>Skapa ditt projekt

Generera ett nytt vinkelprogram med hjälp av följande npm-kommandon:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Registrera ditt program

Följ [instruktionerna för att registrera ett ensidigt program](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) i Azure-portalen.

På sidan App **översikt** av din registrering, notera **application (klient) ID-värdet** för senare användning.

Registrera ditt **Redirect URI-värde** som **http://localhost:4200/** och aktivera implicita bidragsinställningar.

## <a name="configure-the-application"></a>Konfigurera programmet

1. I *src/app-mappen* *redigerar du app.module.ts* och lägger till `MSALModule` `imports` samt konstanten: `isIE`

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
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
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

    Ersätt dessa värden:

    |Värdenamn|Om|
    |---------|---------|
    |Enter_the_Application_Id_Here|På **sidan Översikt för** din ansökan registrering, detta är ditt **Program (klient) ID-värde.** |
    |Enter_the_Cloud_Instance_Id_Here|Detta är instansen av Azure-molnet. För huvud- eller globala **https://login.microsoftonline.com**Azure-molnet anger du . För nationella moln (till exempel Kina), se [Nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
    |Enter_the_Tenant_Info_Here| Ange ett av följande alternativ: Om programmet stöder konton i den *här organisationskatalogen*ersätter du det här värdet med katalog-ID:t (klient)-ID eller klientnamn (till exempel **contoso.microsoft.com**). Om programmet stöder *konton i en organisationskatalog* **ersätter**du det här värdet med organisationer . Om programmet stöder *konton i en organisationskatalog och personliga Microsoft-konton*ersätter du det här värdet med **vanliga**. Om du bara vill begränsa supporten till *personliga Microsoft-konton*ersätter du det här värdet med **konsumenter**. |
    |Enter_the_Redirect_Uri_Here|Ersätt **http://localhost:4200**med .|

    Mer information om tillgängliga konfigurerbara alternativ finns i [Initiera klientprogram](msal-js-initializing-client-applications.md).

2. Lägg till följande importsatsning högst upp i samma fil:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Lägg till följande importsatser `src/app/app.component.ts`högst upp i :

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
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
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

Hämta slutligen en användares profil med en HTTP-begäran:

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
MSAL använder tre metoder för `acquireTokenRedirect` `acquireTokenPopup`att `acquireTokenSilent`hämta token: , och . Vi rekommenderar dock `MsalInterceptor` att du använder klassen i stället för vinkelappar, som visas i föregående avsnitt.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `acquireTokenSilent` hanterar tokenförvärv och förnyelse utan användarinteraktion. När `loginRedirect` metoden `loginPopup` eller körs för första `acquireTokenSilent` gången används ofta för att hämta token som används för att komma åt skyddade resurser i senare anrop. Anrop för att begära eller förnya token görs tyst.

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

I den `scopes` koden innehåller scope som begärs att returneras i åtkomsttoken för API:et.

Ett exempel:

* `["user.read"]`för Microsoft Graph
* `["<Application ID URL>/scope"]`för anpassade webb-API:er (det vill ha) `api://<Application ID>/access_as_user`

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Ibland behöver du användaren för att interagera med slutpunkten för Microsoft identity platform. Ett exempel:

* Användare kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla.
* Ditt program begär åtkomst till ytterligare resursomfattningar som användaren behöver samtycka till.
* Tvåfaktorsautentisering krävs.

Det rekommenderade mönstret för `acquireTokenSilent` de flesta program är att `acquireTokenPopup` ringa `acquireTokenRedirect`först, sedan fånga undantaget och sedan ringa (eller) för att starta en interaktiv begäran.

Att `acquireTokenPopup` ringa resulterar i ett popup-fönster. Du kan `acquireTokenRedirect` också omdirigera användare till slutpunkten för Microsoft identity platform. I det fönstret måste användarna bekräfta sina autentiseringsuppgifter, ge medgivande till den nödvändiga resursen eller slutföra tvåfaktorsautentisering.

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
> Den här snabbstarten använder metoderna `loginRedirect` och `acquireTokenRedirect` med Microsoft Internet Explorer på grund av ett känt [problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) som är relaterat till hantering av popup-fönster av Internet Explorer.

## <a name="log-out"></a>Logga ut

Lägg till följande kod för att logga ut en användare:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Lägga till användargränssnitt
Ett exempel på hur du lägger till användargränssnitt med hjälp av komponentbiblioteket Vinkelmaterial finns i [exempelprogrammet](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>Testa koden

1.  Starta webbservern för att lyssna på porten genom att köra följande kommandon vid en kommandoradsfråga från programmappen:

    ```bash
    npm install
    npm start
    ```
1. I webbläsaren anger **http://localhost:4200** **http://localhost:{port}** eller , där *porten* är den port som webbservern lyssnar på.


### <a name="provide-consent-for-application-access"></a>Ge samtycke till tillgång till program

Första gången du börjar logga in på ditt program uppmanas du att ge den åtkomst till din profil och låta den logga in:

![Fönstret "Begärda behörigheter"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)



<!--start-collapse-->
### <a name="add-scopes-and-delegated-permissions"></a>Lägga till scope och delegerade behörigheter

Microsoft Graph API kräver *att användaren.läser* omfång för att läsa en användares profil. Som standard läggs det här scopet automatiskt till i alla program som är registrerade på registreringsportalen. Andra API:er för Microsoft Graph, samt anpassade API:er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver till exempel *scopet Calendars.Read* för att kunna visa användarens kalendrar.

Om du vill komma åt användarens kalendrar i ett program lägger du till den delegerade behörigheten *Calendars.Read* i programregistreringsinformationen. Lägg sedan till *calendars.Read-scopet* i anropet. `acquireTokenSilent`

>[!NOTE]
>Användaren kan uppmanas att ange ytterligare medgivanden när du ökar antalet scope.

Om ett backend-API inte kräver ett scope (rekommenderas inte) kan du använda *clientId* som scope i anropen för att hämta token.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig sedan hur du loggar in en användare och hämtar token i den kantiga självstudien:

> [!div class="nextstepaction"]
> [Vinkel handledning](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)
