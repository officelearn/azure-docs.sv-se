---
title: Azure AD AngularJS komma igång | Microsoft Docs
description: 'Hur du skapar en enkel sida AngularJS-program som kan integreras med Azure AD för inloggning och anropar Azure AD-skyddade API: er med hjälp av OAuth.'
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
ms.openlocfilehash: 5b99ce605d9ecea6c7d67ab9a2ea679d531787d7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-angularjs-getting-started"></a>Azure AD AngularJS komma igång

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (AD Azure) gör det lätt att lägga till inloggning, utloggning och säker OAuth-API anrop till en sida-appar. Det gör att dina appar att autentisera användare med sina Windows Server Active Directory-konton och använda en webb-API som Azure AD hjälper till att skydda, till exempel API: er för Office 365 eller Azure API.

För JavaScript-program som körs i en webbläsare, innehåller Azure AD Active Directory Authentication Library (ADAL) eller adal.js. Det enda syftet med adal.js är att göra det lättare för din app för att få åtkomst-token. För att demonstrera hur lätt det är ska här vi skapa ett program med AngularJS att göra-lista som:

* Loggar användaren in i appen med Azure AD som identitetsleverantören.

* Visar information om användaren.
* Anropar appens att göra listan API: N på ett säkert sätt med hjälp av ägar-token från Azure AD.
* Loggar du ut från appen.

Om du vill skapa klar fungerande program måste du:

1. Registrera din app med Azure AD.
2. Installera ADAL och konfigurera en sida-app.
3. Använd ADAL för att säkra sidor i en sida-app.

Du kommer igång [ladda ned stommen app](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) eller [hämta det slutförda exemplet](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Du måste också en Azure AD-klient som du kan skapa användare och registrera ett program. Om du inte redan har en klient [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Steg 1: Registrera DirectorySearcher-program
Om du vill aktivera din app för att autentisera användare och hämta token måste du först registrera det i Azure AD-klient:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om du är inloggad på flera kataloger kan du behöva se till att du visar rätt katalog. Om du vill göra det, på den översta raden, klickar du på ditt konto. Under den **Directory** Välj Azure AD-klient som du vill registrera ditt program.
3. Klicka på **alla tjänster** i det vänstra fönstret och välj sedan **Azure Active Directory**.
4. Klicka på **App registreringar**, och välj sedan **Lägg till**.
5. Följ anvisningarna och skapa ett nytt webbprogram och/eller webb-API:
  * **Namnet** beskriver ditt program till användare.
  * **URL: en inloggning** är den plats som Azure AD som returneras av token. Standardplatsen för det här exemplet är `https://localhost:44326/`.
6. När du har slutfört registreringen tilldelar ett unikt ID i Azure AD till din app. Du behöver det här värdet i nästa avsnitt så kopiera den från programfliken.
7. Adal.js använder implicita flödet för OAuth för att kommunicera med Azure AD. Du måste aktivera det implicita flödet för programmet:
  1. Klicka på programmet och välj **Manifest** att öppna redigeraren infogade manifestet.
  2. Leta upp den `oauth2AllowImplicitFlow` egenskapen. Ange värdet till `true`.
  3. Klicka på **spara** spara manifestet.
8. Ge din klient för ditt program. Gå till **inställningar** > **nödvändiga behörigheter**, och klicka på den **bevilja med** knappen i det översta fältet. Bekräfta genom att klicka på **Ja**.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Steg 2: Installera ADAL och konfigurera en sida-app
Nu när du har ett program i Azure AD kan du installera adal.js och Skriv koden identitetsrelaterade.

### <a name="configure-the-javascript-client"></a>Konfigurera JavaScript-klienten
Börja genom att lägga till adal.js TodoSPA-projekt med hjälp av Package Manager-konsolen:
  1. Hämta [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) och lägger till den i den `App/Scripts/` projektkatalogen.
  2. Hämta [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) och lägger till den i den `App/Scripts/` projektkatalogen.
  3. Läsa in varje skript före utgången av den `</body>` i `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurera backend-servern
För sida appens backend-att göra listan API att acceptera token från webbläsaren, måste serverdelen konfigurationsinformation om appregistrering. Öppna i projektet TodoSPA `web.config`. Ersätt värdena för elementen i den `<appSettings>` avsnittet för att återspegla de värden som du använde i Azure-portalen. Koden ska referera till dessa värden när den använder ADAL.
  * `ida:Tenant` är domänen för din Azure AD-klient – till exempel contoso.onmicrosoft.com.
  * `ida:Audience` är klient-ID för programmet som du kopierade från portalen.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Steg 3: Använda ADAL för att säkra sidor i appen en sida
Adal.js integreras med AngularJS flödes- och HTTP-providers, så kan du säkra enskilda vyer i din app på en sida.

1. I `App/Scripts/app.js`, sätta i modulen adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Initiera `adalProvider` med hjälp av konfigurationsvärden för programmet registreringen, även i `App/Scripts/app.js`:

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
3. Skydda den `TodoList` visa i appen med hjälp av en enda rad med kod: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Sammanfattning
Nu har du en säker sida-app som kan logga in användare och utfärda ägar-token-skyddade begäranden till dess backend-API. När användaren klickar på **TodoList** länk adal.js omdirigeras automatiskt till Azure AD för inloggning om det behövs. Dessutom kommer adal.js automatiskt koppla en åtkomst-token till Ajax-begäranden som skickas till appens serverdel. 

Föregående steg är att skapa en enkel sida-app med hjälp av adal.js utan minsta nödvändiga. Men några andra funktioner som är användbara i en sida app:

* Om du vill utfärda explicit logga in och utloggningsförfrågningar, kan du definiera funktioner i din domänkontrollanter som anropar adal.js. I `App/Scripts/homeCtrl.js`:

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
* Du kanske vill presentera användarinformation i appens användargränssnitt. ADAL-tjänsten har redan lagts till i `userDataCtrl` styrenhet, så du kan komma åt den `userInfo` objekt i den associerade vyn `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Det finns många scenarier där du vill veta om användaren är inloggad eller inte. Du kan också använda den `userInfo` objekt att samla in informationen. Till exempel på `index.html`, du kan visa den **inloggning** eller **logga ut** knappen baserat på autentiseringsstatus:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Din Azure AD-integrerade single-page-app kan autentisera användare, på ett säkert sätt anropa sina serverdelens med hjälp av OAuth 2.0 och få grundläggande information om användaren. Om du inte redan gjort nu är det dags att fylla i din klient med vissa användare. Kör appen att göra-lista sida och logga in med något av dessa användare. Lägg till aktiviteter i användarens att göra-lista, logga ut och logga in igen.

Adal.js gör det enkelt att införliva vanliga identity-funktioner i programmet. Det tar hand om ändrad arbetet åt dig: hantering av cache, OAuth protokollstöd presentera användaren med en inloggning användargränssnitt, uppdatera token har upphört att gälla och mycket mer.

För referens anger det slutförda exemplet (utan dina konfigurationsvärden) är tillgängligt i [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Nästa steg
Du kan nu gå vidare till fler scenarier. Du kanske vill prova: [anropa CORS-webb-API från en enda sida app](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
