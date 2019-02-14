---
title: Skapa en enkelsidig AngularJS-webbapp för inloggning och utloggning med Azure Active Directory | Microsoft Docs
description: Lär dig hur du skapar ett enkelsidigt AngularJS-program som integreras med Azure AD för inloggning och anropar Azure AD-skyddade API:er med hjälp av OAuth.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c508c402707663489fc383dccd958559df6b217f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194690"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Snabbstart: Skapa en enkelsidig AngularJS-webbapp för inloggning och utloggning med Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (AD Azure) gör det enkelt att lägga till inloggning, utloggning och säkra OAuth API-anrop till enkelsidiga appar. Det gör att dina appar kan autentisera användare med deras Windows Server Active Directory-konton och använda valfritt webb-API som Azure AD hjälper till att skydda, till exempel Office 365-API:er eller Azure API.

För JavaScript-program som körs i en webbläsare tillhandahåller Azure AD Active Directory Authentication Library (ADAL), eller adal.js. Det enda syftet med adal.js är att göra det enkelt för din app för att få åtkomsttoken.

I den här snabbstarten lär du dig hur du skapar ett att göra-program med AngularJS som:

* Loggar in användaren till appen med hjälp av Azure AD som identitetsprovider.
* Visar viss information om användaren.
* På ett säkert sätt anropar appens att göra-API med hjälp av ägartoken från Azure AD.
* Loggar ut användaren ur appen.

Om du vill skapa ett komplett, fungerande program måste du:

1. Registrera appen med Azure AD.
2. Installera ADAL och konfigurera ensidesappen.
3. Använd ADAL för att säkra sidor i ensidesappen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom igång genom att slutföra följande krav:

* [Ladda ned appens stomme](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) eller [ladda ned det färdiga exemplet](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Ha en Azure AD-klientorganisation där du kan skapa användare och registrera en app. Om du inte redan har en klientorganisation kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Steg 1: Registrera DirectorySearcher-programmet

Om du vill göra så att appen kan autentisera användare och hämta token behöver du först registrera den i din Azure AD-klientorganisation:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Om du är inloggad i flera kataloger kan du behöva se till att du är i rätt katalog. Gör det genom att klicka på ditt konto i fältet längst upp. Under **kataloglistan** väljer du den Azure AD-klientorganisation där du vill registrera programmet.
1. Klicka på **Alla tjänster** i den vänstra fönsterrutan och välj sedan **Azure Active Directory**.
1. Klicka på **Appregistreringar** och välj sedan **Lägg till**.
1. Följ anvisningarna och skapa ett nytt webbprogram och/eller webb-API:

    * **Namn** beskriver appen för dina användare.
    * **Inloggnings-URL** är den plats som Azure AD returnerar token till. Standardplatsen för det här exemplet är `https://localhost:44326/`.

1. När du har slutfört registreringen tilldelar Azure AD ett unikt ID till din app. Du behöver det här värdet i nästa avsnitt, så kopiera det från appfliken.
1. Adal.js använder det implicita flödet för OAuth för att kommunicera med Azure AD. Du måste aktivera det implicita flödet för ditt program:

    1. Klicka på programmet och välj **Manifest** för att öppna redigeraren för infogade manifest.
    1. Leta rätt på egenskapen `oauth2AllowImplicitFlow`. Ställ in värdet på `true`.
    1. Klicka på **Spara** för att spara manifestet.

1. Ge behörigheter till hela klientorganisationen för programmet. Gå till **Inställningar > Nödvändiga behörigheter** och välj knappen **Bevilja behörigheter** i det översta fältet.
1. Bekräfta genom att välja **Ja**.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Steg 2: Installera ADAL och konfigurera den enkelsidiga appen

Nu när du har ett program i Azure AD kan du installera adal.js och skriva din identitetsrelaterade kod.

### <a name="configure-the-javascript-client"></a>Konfigurera JavaScript-klienten

Börja genom att lägga till adal.js i TodoSPA-projektet med hjälp av Package Manager-konsolen:

1. Ladda ned [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) och lägg till det i projektkatalogen `App/Scripts/`.
2. Ladda ned [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) och lägg till det i projektkatalogen `App/Scripts/`.
3. Läs in varje skript före slutet av `</body>` i `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurera backend-servern

För att ensidesappens serverdels att göra-API ska kunna acceptera token från webbläsaren behöver serverdelen konfigurationsinformation om appregistreringen. I TodoSPA-projektet öppnar du `web.config`. Ersätt värdena för elementen i avsnittet `<appSettings>` så att de speglar de värden som du använde i Azure-portalen. Koden kommer att referera till dessa värden när den använder ADAL.

   * `ida:Tenant` är domänen för din Azure AD-klientorganisation – till exempel contoso.onmicrosoft.com.
   * `ida:Audience` är det klient-ID för din app som du kopierade från portalen.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Steg 3: Använda ADAL för att skydda sidor i den enkelsidiga appen

Adal.js integreras med AngularJS-vägen och HTTP-providers, så du kan hjälpa till att säkra enskilda vyer i din ensidesapp.

1. I `App/Scripts/app.js` tar du med adal.js-modulen:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Initiera `adalProvider` genom att använda konfigurationsvärdena för din programregistrering, även i `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Skydda `TodoList`-vyn i appen med hjälp av endast en kodrad: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Sammanfattning

Nu har du en säker ensidesapp som kan logga in användare och skicka ägartokenskyddade begäranden till sitt serverdels-API. När en användare klickar **TodoList**-länken omdirigera adal.js automatiskt till Azure AD för inloggning om det behövs. Dessutom kommer adal.js att automatiskt koppla en åtkomsttoken till alla Ajax-begäranden som skickas till appens serverdel.

Föregående steg är minimikraven för att skapa en ensidesapp med hjälp av adal.js. Det finns dock några andra funktioner som är användbara i ensidesapp:

* För att explicit utfärda inloggnings-och utloggningsbegäranden kan du definiera funktioner i de styrenheter som anropar adal.js. Följande gäller i `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Det kan vara bra att visa användarinformation i appens användargränssnitt. ADAL-tjänsten har redan lagts till i `userDataCtrl`-styrenheten, så du kan komma åt objektet `userInfo` i den associerade vyn `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Det finns många scenarier där du vill veta om användaren är inloggad eller inte. Du kan även använda objektet `userInfo` för att samla in den här informationen. Till exempel kan du i `index.html` visa antingen knappen **Logga in** eller **Logga ut** baserat på autentiseringsstatus:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Din Azure AD-integrerade ensidesapp kan autentisera användare, anropa sin backend-server på ett säkert sätt med hjälp av OAuth 2.0 och få grundläggande information om användaren. Om du inte redan har gjort det är det dags att fylla i klientorganisationen med några användare. Kör din att göra-ensidesapp och logga in med någon av dessa användare. Lägg till aktiviteter i användarens att göra-lista, logga ut och logga in igen.

Adal.js gör det enkelt att inkorporera vanliga identitetsfunktioner i appen. Det tar hand om allt grovjobb åt dig: cachehantering, stöd för OAuth-protokoll, visa användaren ett användargränssnitt för inloggning, uppdatera token som gått ut och mer.

Som referens finns det slutförda exemplet (utan dina konfigurationsvärden) på [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med fler scenarier.

> [!div class="nextstepaction"]
> [Anropa ett CORS-webb-API från en ensidesapp](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
