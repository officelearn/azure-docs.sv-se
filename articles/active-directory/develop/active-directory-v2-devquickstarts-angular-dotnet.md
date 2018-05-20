---
title: Azure AD v2.0 .NET AngularJS sida-app komma igång | Microsoft Docs
description: Hur du skapar en vinkel JS sida-app som loggar in användare med både personliga Microsoft-konton och arbets-eller skolkonton.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6a341781-278f-461b-92ca-7572a06e6852
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1d1ddeb957096dc506bc76e1aac600eaa5bc2fbc
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="add-sign-in-to-an-angularjs-single-page-app---net"></a>Lägga till inloggning till en AngularJS sida app - .NET
I den här artikeln ska vi lägga till logga in med Microsoft påslagen konton för en AngularJS-app med Azure Active Directory v2.0-slutpunkten.  V2.0-slutpunkten kan du utföra en enkel integrering i din app och autentiserar användare med både personliga och arbete/skola konton.

Det här exemplet är en enkel sida app för att göra-lista som lagrar uppgifter i en serverdel REST API, som skrivits i .NET 4.5 MVC framework och skyddas med OAuth ägar-token från Azure AD.  AngularJS-appen kommer att använda våra öppen källkod JavaScript-autentiseringsbibliotek [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) kan hantera hela tecknet i processen och hämta token för att anropa REST-API.  Samma mönster som kan användas för att autentisera till andra REST-API: er, som den [Microsoft Graph](https://graph.microsoft.com).

> [!NOTE]
> Inte alla Azure Active Directory-scenarier och funktioner som stöds av v2.0-slutpunkten.  Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

## <a name="download"></a>Ladda ned
Om du vill komma igång, måste du hämta och installera Visual Studio.  Sedan kan du klona eller [hämta](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) stommen app:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

Appen stommen innehåller formaterad exempelkod för en enkel AngularJS-app, men saknar alla identitetsrelaterade bitar.  Om du inte vill att följa instruktionerna du i stället klona eller [hämta](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) det slutförda exemplet.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## <a name="register-an-app"></a>Registrera en app
Först skapar du en app i den [App Registreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följa dessa [detaljerade steg](active-directory-v2-app-registration.md).  Se till att:

* Lägg till den **Web** plattform för din app.
* Ange rätt **omdirigerings-URI**. Standardvärdet för det här exemplet är `https://localhost:44326/`.
* Lämna den **Tillåt Implicit flöda** kryssrutan aktiverad. 

Kopiera den **program-ID** som har tilldelats din app behöver du den inom kort. 

## <a name="install-adaljs"></a>Installera adal.js
Starta genom att gå till projektet du hämtade och installera adal.js.  Om du har [bower](http://bower.io/) installerat, du kan bara köra det här kommandot.  Välj den högre versionen för alla beroende version avvikelser.

```
bower install adal-angular#experimental
```

Alternativt kan du manuellt hämta [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) och [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Lägg till båda filer i den `app/lib/adal-angular-experimental/dist` för den `TodoSPA` projekt.

Nu öppna projektet i Visual Studio och läsa in adal.js i slutet av sidan huvudsakliga brödtext:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Konfigurera REST API
Medan vi konfigurerar saker, ska få fungerande för backend-REST API.  Öppna i roten av projektet `web.config` och Ersätt den `audience` värde.  REST API använder det här värdet för att validera token som tas emot från appen vinkel på AJAX-begäranden.

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>

...
```

Det är den tid som ska vi använda diskutera hur REST API fungerar.  Passa på att undersöka i koden, men om du vill veta mer om att skydda webb-API: er med Azure AD, kolla [i den här artikeln](active-directory-v2-devquickstarts-dotnet-api.md). 

## <a name="sign-users-in"></a>Logga in användare
Tid för att skriva kod identitet.  Du kanske redan har upptäckt att adal.js innehåller en AngularJS-provider som spelas snyggt vinkel routning mekanismer.  Starta genom att lägga till modulen adal appen:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Du kan nu initiera den `adalProvider` med program-ID:

```js
// app/scripts/app.js

...

adalProvider.init({

        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 

        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',

        // Your application id from the registration portal
        clientId: '<Your-application-id>',

        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',

    }, $httpProvider);
```

Bra, nu adal.js har all information som behövs för att skydda din app och inloggning av användare.  Om du vill tvinga inloggningen för ett visst flöde i appen, behöver du bara en rad med kod:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Nu när användaren klickar på `TodoList` länk adal.js omdirigeras automatiskt till Azure AD för inloggning om det behövs.  Du kan också explicit skicka inloggning och utloggning begäranden genom att anropa adal.js i dina domänkontrollanter:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {

        // Redirect the user to sign in
        adalService.login();

    };
    $scope.logout = function () {

        // Redirect the user to log out    
        adalService.logOut();

    };
...
```

## <a name="display-user-info"></a>Visa användarinformation
Nu när användaren är inloggad, måste du förmodligen att komma åt den inloggade användarens autentiseringsdata i ditt program.  Adal.js visar den här informationen för dig i den `userInfo` objekt.  För att komma åt det här objektet i en vy, först lägga till adal.js rot omfånget för motsvarande domänkontrollant:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Sedan direkt kan du lösa det `userInfo` objekt i vyn: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Du kan också använda den `userInfo` objektet för att avgöra om användaren är inloggad eller inte.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Anropa REST-API
Slutligen är det dags att hämta en token och anropa REST-API för att skapa, läsa, uppdatera och ta bort uppgifter.  Väl vet du vad?  Du behöver inte *något*.  Adal.js hand automatiskt tar om komma cachelagring och uppdatera token.  Den också hand tar om att de tokens till utgående AJAX-begäranden som du skickar till REST API.  

Hur fungerar det? Det är alla tack vare Magiskt tal för [AngularJS interceptorerna](https://docs.angularjs.org/api/ng/service/$http), vilket gör att adal.js att omvandla inkommande och utgående HTTP-meddelanden.  Adal.js förutsätter dessutom att skicka förfrågningar till samma domän som fönstret ska använda token är avsedda för samma program-ID som AngularJS-app.  Det är därför vi används samma program-ID i vinkel appen och i NodeJS REST API.  Du kan åsidosätta detta beteende och berätta adal.js att hämta token för andra REST API: er om det behövs - men med det här enkla scenariot standardvärdena gör.

Här är ett kodfragment som visar hur lätt det är att skicka begäranden med ägar-token från Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Grattis!  Appen Azure AD-integrerade sida är slutförd.  Gå vidare, bugar.  Den kan autentisera användare, på ett säkert sätt anropa sina serverdelens REST-API med OpenID Connect och få grundläggande information om användaren.  Out of box, den har stöd för alla användare med ett personligt Microsoft-Account eller arbete/skolkonto från Azure AD.  Kör appen och i en webbläsare navigerar du till `https://localhost:44326/`.  Logga in med ett personligt microsoftkonto eller arbete/skolkonto.  Lägg till aktiviteter i användarens att göra-lista och logga ut.  Försök att logga in med typ av konto. Om du behöver en Azure AD-klient för att skapa arbete/skola användare [Lär dig hur du skaffa en här](active-directory-howto-tenant.md) (det är ledigt).

Om du vill fortsätta lära dig mer om v2.0-slutpunkten head tillbaka till våra [v2.0 Utvecklarhandbok](active-directory-appmodel-v2-overview.md).  För ytterligare resurser, kolla:

* [Azure-exemplen på GitHub >>](https://github.com/Azure-Samples)
* [Azure AD på stackspill >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
* Azure AD-dokumentationen på [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Hämta säkerhetsuppdateringar för våra produkter
Vi rekommenderar att du aktiverar aviseringar om säkerhetsincidenter genom att gå till [den här sidan](https://technet.microsoft.com/security/dd252948) och prenumerera på Microsoft Security Advisory-aviseringar.

