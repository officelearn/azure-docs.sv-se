---
title: 'Självstudiekurs: Bevilja åtkomst till ett webb-API för ASP.NET kärna från en ensidig app'
titleSuffix: Azure AD B2C
description: I den här självstudien kan du lära dig hur du använder Active Directory B2C för att skydda ett .NET Core-webb-API och anropa API:et från ett node.js-program med en sida.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f6f9ff7bb0d504ecc163f6ce1f87477b1ea9c2d1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186158"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Självstudiekurs: Bevilja åtkomst till ett ASP.NET Core-webb-API från ett ensidigt program med Azure Active Directory B2C

Den här självstudien visar hur du anropar en Azure Active Directory B2C (Azure AD B2C)-skyddad ASP.NET Core-webb-API-resurs från ett ensidigt program.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Konfigurera exemplet för att använda programmet

## <a name="prerequisites"></a>Krav

* Slutför stegen och förutsättningarna i [självstudiekurs: Aktivera autentisering i ett ensidigt program med Azure Active Directory B2C](tutorial-single-page-app.md).
* Visual Studio 2019 eller senare, eller Visual Studio-kod
* .NET Core 2.2 eller senare
* Node.js

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du både läs- och skrivbehörighet för webb-API:et.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registrera värdet under **SCOPES** som `demo.read` scopet ska använda i ett senare steg när du konfigurerar ett enda sidbaserat program. Det fullständiga scopevärdet `https://contosob2c.onmicrosoft.com/api/demo.read`liknar .

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett annat program måste du bevilja programbehörigheterna till webb-API:et.

I den nödvändiga självstudien skapade du ett webbprogram med namnet *webapp1*. I den här självstudien konfigurerar du programmet så att det anropar webb-API:et som du skapade i ett tidigare avsnitt, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Ditt ensidiga webbprogram är registrerat för att anropa det skyddade webb-API:et. En användare autentiserar med Azure AD B2C för att använda ensidiga program. Den ensidiga appen erhåller ett auktoriseringsbidrag från Azure AD B2C för att komma åt det skyddade webb-API:et.

## <a name="configure-the-sample"></a>Konfigurera exemplet

När webb-API:et är registrerat och har ett definierat omfång måste du konfigurera webb-API-koden så den använder din Azure AD B2C-klientorganisation. I den här självstudien konfigurerar du ett exempel på ett EXEMPEL PÅ .NET Core-webbprogram som du hämtar från GitHub.

[Hämta \*ett ZIP-arkiv](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) eller klona exempelweet-projektet från GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurera webb-API

1. Öppna filen <em>B2C-WebApi/**appsettings.json** </em> i Visual Studio eller Visual Studio Code.
1. Ändra `AzureAdB2C` blocket för att återspegla ditt klientnamn, program-ID för webb-API-programmet, namnet på din registrerings-/inloggningsprincip och de scope som du definierade tidigare. Blocket ska se ut ungefär som `Tenant` i `ClientId` följande exempel (med lämpliga värden och värden):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>Aktivera CORS

Om du vill att ditt ensidiga program ska kunna anropa webb-API:et för ASP.NET Core måste du aktivera [CORS](https://docs.microsoft.com/aspnet/core/security/cors) i webb-API:et.

1. Lägg till CORS till metoden `ConfigureServices()` i *Startup.cs*.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. `ConfigureServices()` Ange även `jwtOptions.Authority` värdet till följande tokenutfärdare URI.

    Ersätt `<your-tenant-name>` med namnet på din B2C-klient.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. Konfigurera `Configure()` CORS i metoden.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Endast Visual Studio) Öppna *filen launchSettings.json* under **Egenskaper** i Solution Explorer `iisExpress` och leta sedan reda på blocket.
1. (Endast Visual Studio) Uppdatera `applicationURL` värdet med det portnummer som du angav när du registrerade *webapi1-programmet* i ett tidigare steg. Ett exempel:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Konfigurera ett ensidigt program

Det ensidiga programmet (SPA) från föregående [självstudie i](tutorial-single-page-app.md) serien använder Azure AD B2C för användares registrering och inloggning, och anropar ASP.NET Core-webb-API som skyddas av *frabrikamb2c-demoklienten.*

I det här avsnittet uppdaterar du det ensidiga programmet för att anropa ASP.NET Core-webb-API som skyddas av *din* Azure AD B2C-klientorganisation och som du kör på din lokala dator.

Så här ändrar du inställningarna i SPA:et:

1. Öppna *filen index.html* i projektet [active-directory-b2c-javascript-msal-singlepageapp som][github-js-spa] du hämtade eller klonade i föregående självstudie.
1. Konfigurera exemplet med URI för *det demo.read-scope* som du skapade tidigare och url:en för webb-API:et.
    1. I `appConfig` definitionen ersätter `b2cScopes` du värdet med hela URI för scopet **(SCOPE-värdet** som du registrerade tidigare).
    1. Ändra `webApi` värdet till den omdirigera URI som du lade till när du registrerade webb-API-programmet i ett tidigare steg.

    Definitionen `appConfig` ska se ut ungefär som följande kodblock (med `<your-tenant-name>`ditt klientnamn på platsen för):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Kör SPA- och webb-API:et

Slutligen kör du både webb-API:et för ASP.NET core och node.js-programmet på den lokala datorn. Sedan loggar du in på det ensidiga programmet och trycker på en knapp för att initiera en begäran till det skyddade API:et.

Även om båda programmen körs lokalt i den här självstudien använder de Azure AD B2C för säker registrering/inloggning och för att bevilja åtkomst till det skyddade webb-API:et.

### <a name="run-the-aspnet-core-web-api"></a>Kör webb-API:et för ASP.NET Core

I Visual Studio trycker du på **F5** för att skapa och felsöka *B2C-WebAPI.sln-lösningen.* När projektet startas visas en webbsida i standardwebbläsaren som meddelar att webb-API:et är tillgängligt för begäranden.

Om du föredrar `dotnet` att använda CLI istället för Visual Studio:

1. Öppna ett konsolfönster och ändra * \** till katalogen som innehåller CSproj-filen. Ett exempel:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Skapa och kör webb-API:et genom att `dotnet run`köra .

    När API:et är igång bör du se utdata som liknar följande (för självstudien kan du säkert ignorera alla `NETSDK1059` varningar):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Kör ensidesappen

1. Öppna ett konsolfönster och ändra till katalogen som innehåller exemplet Node.js. Ett exempel:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Kör följande kommandon:

    ```console
    npm install && npm update
    node server.js
    ```

    Konsolfönstret visar portnumret där programmet finns.

    ```console
    Listening on port 6420...
    ```

1. Navigera `http://localhost:6420` till i webbläsaren för att visa programmet.
1. Logga in med den e-postadress och det lösenord som du använde i [föregående självstudie .](tutorial-single-page-app.md) Vid lyckad inloggning bör `User 'Your Username' logged-in` du se meddelandet.
1. Välj knappen **Anropa webb-API.** SPA erhåller ett auktoriseringsbidrag från Azure AD B2C och får sedan åtkomst till det skyddade webb-API:et för att visa innehållet på dess indexsida:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Konfigurera exemplet för att använda programmet

Nu när du har sett en SPA-begäran om en resurs från ett skyddat webb-API får du en djupare förståelse för hur dessa programtyper interagerar med varandra och med Azure AD B2C.

> [!div class="nextstepaction"]
> [Programtyper som kan användas i Active Directory B2C->](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
