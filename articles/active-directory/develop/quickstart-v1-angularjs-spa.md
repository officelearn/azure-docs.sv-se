---
title: Azure AD-AngularJS komma igång | Microsoft Docs
description: 'Hur du skapar ett AngularJS enkelsidigt program som integreras med Azure AD för att logga in och anropar Azure AD-skyddade API: er med hjälp av OAuth.'
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0c7f6a0e447e3b48cdd1df684dc105ece1e98f66
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581928"
---
# <a name="azure-ad-angularjs-getting-started"></a>Azure AD-AngularJS komma igång

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (AD Azure) gör det enkelt och enkelt du kan lägga till inloggning, utloggning och säker OAuth-API-anrop till enkelsidiga appar. Det gör att dina appar att autentisera användare med sina Windows Server Active Directory-konton och använda alla webb-API som Azure AD hjälper dig att skydda, till exempel Office 365 API: er eller Azure-API.

Azure AD tillhandahåller Active Directory Authentication Library (ADAL) eller adal.js för JavaScript-program som körs i en webbläsare. Det enda syftet adal.js är att göra det enkelt för din app för att få åtkomst-token. För att demonstrera hur enkelt det är ska här vi skapa ett program med AngularJS att göra-lista som:

* Loggar du in på appen med hjälp av Azure AD som identitetsleverantör.

* Visar information om användaren.
* På ett säkert sätt anropar appens att göra-lista API: N med ägar-token från Azure AD.
* Loggar ut användaren från appen.

Om du vill skapa klar fungerande-program, måste du:

1. Registrera din app med Azure AD.
2. Installera ADAL och konfigurera ensidesapp.
3. Använd ADAL för att säkra sidor i enkelsidigt program.

Du kommer igång [ladda ned stommen app](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) eller [hämta det färdiga exemplet](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Du behöver också en Azure AD-klient som du kan skapa användare och registrera ett program. Om du inte redan har en klient, [Lär dig hur du skaffa ett](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Steg 1: Registrera DirectorySearcher-program
Om du vill aktivera din app för att autentisera användare och hämta token, måste du först registrera det i Azure AD-klienten:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om du är inloggad på flera kataloger, kan du behöva se till att du visar rätt katalog. Klicka på ditt konto om du vill göra det, i det översta fältet. Under den **Directory** väljer Azure AD-klient där du vill registrera ditt program.
3. Klicka på **alla tjänster** i den vänstra rutan och välj sedan **Azure Active Directory**.
4. Klicka på **appregistreringar**, och välj sedan **Lägg till**.
5. Följ anvisningarna och skapa ett nytt webbprogram och/eller webb-API:
  * **Namn på** beskriver programmet för användare.
  * **Inloggnings-URL** är den plats som Azure AD returnerar token. Standardplatsen för det här exemplet är `https://localhost:44326/`.
6. När du har slutfört registreringen tilldelar ett unikt ID i Azure AD till din app. Du behöver det här värdet i nästa avsnitt, så kopiera den från programfliken.
7. Adal.js använder det implicita flödet för OAuth för att kommunicera med Azure AD. Du måste aktivera det implicita flödet för ditt program:
  1. Klicka på programmet och välj **Manifest** att öppna redigeraren infogade manifest.
  2. Leta upp den `oauth2AllowImplicitFlow` egenskapen. Ange värdet till `true`.
  3. Klicka på **spara** att spara manifestet.
8. Ge din klient för ditt program. Gå till **inställningar** > **nödvändiga behörigheter**, och klicka på den **bevilja behörigheter** knappen i det översta fältet. Bekräfta genom att klicka på **Ja**.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Steg 2: Installera ADAL och konfigurera enkelsidiga appar
Nu när du har ett program i Azure AD kan du installera adal.js och skriva koden identitetsrelaterade.

### <a name="configure-the-javascript-client"></a>Konfigurera JavaScript-klient
Börja genom att lägga till adal.js TodoSPA-projekt med hjälp av Package Manager-konsolen:
  1. Ladda ned [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) och lägger till den i den `App/Scripts/` projektkatalogen.
  2. Ladda ned [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) och lägger till den i den `App/Scripts/` projektkatalogen.
  3. Läsa in varje skript före utgången av den `</body>` i `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurera backend-servern
Serverdelen måste konfigurationsinformation om registreringen för enkelsidiga appens backend-att göra lista API accepterar token från webbläsaren. Öppna i projektet TodoSPA `web.config`. Ersätt värdena för elementen i den `<appSettings>` avsnitt för att återspegla de värden som du använde i Azure-portalen. Koden kommer att använda dessa värden när den använder ADAL.
  * `ida:Tenant` är domänen i Azure AD-klienten – till exempel contoso.onmicrosoft.com.
  * `ida:Audience` är klient-ID för programmet som du kopierade från portalen.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Steg 3: Använda ADAL för att säkra sidor i enkelsidig app
Adal.js integreras med AngularJS-väg och HTTP-leverantörer, så du kan hjälpa att säkra enskilda vyer i dina program.

1. I `App/Scripts/app.js`, ta med i modulen adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Initiera `adalProvider` med konfigurationsvärden i din programregistrering också i `App/Scripts/app.js`:

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
3. Skydda den `TodoList` vy i appen genom att använda endast en rad med kod: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Sammanfattning
Nu har du en säker enkelsidigt program som kan logga in användare och skicka ägar-token-skyddade begäranden till dess serverdels-API. När en användare klickar på **TodoList** länken, adal.js omdirigeras automatiskt till Azure AD för att logga in om det behövs. Dessutom kommer adal.js automatiskt koppla en åtkomsttoken till alla Ajax-begäranden som skickas till appens serverdel. 

Föregående steg är att skapa en ensidesapp med hjälp av adal.js utan minsta nödvändiga. Men några andra funktioner är användbara i ensidesapp:

* För att utfärda inloggnings-och utloggning uttryckligen, kan du definiera functions i kontrollenheterna som anropar adal.js. I `App/Scripts/homeCtrl.js`:

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
* Du kan visa användarinformation i appens användargränssnitt. ADAL-tjänsten har redan lagts till i `userDataCtrl` styrenhet, så att du kan komma åt den `userInfo` objekt i den associerade vyn `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Det finns många scenarier där du vill veta om användaren är inloggad eller inte. Du kan också använda den `userInfo` objekt för att samla in den här informationen. Till exempel på `index.html`, du kan visa den **inloggning** eller **utloggning** knappen baserat på autentiseringsstatus för:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Din Azure AD-integrerade program kan autentisera användare, på ett säkert sätt anropa backend-server med hjälp av OAuth 2.0 och få grundläggande information om användaren. Om du inte redan gjort nu är det hög tid att fylla i din klient med vissa användare. Kör din att göra-lista ensidesapp och logga in med något av dessa användare. Lägga till aktiviteter till användarens att göra-lista, logga ut och logga in igen.

Adal.js gör det enkelt att införliva vanliga identitetsfunktioner i ditt program. Det tar hand om ändrad arbetet åt dig: hantering av cache, stöd för OAuth-protokoll, presentera användaren med en inloggning användargränssnitt, uppdatera utgångna token och mycket mer.

Referens är färdiga exemplet (utan dina konfigurationsvärden) är tillgänglig i [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Nästa steg
Du kan nu gå vidare till fler scenarier. Du kanske vill prova: [anropa ett webb-CORS API från en ensidesapp](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
