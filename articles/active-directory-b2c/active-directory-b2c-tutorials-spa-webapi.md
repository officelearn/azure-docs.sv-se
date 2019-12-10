---
title: 'Självstudie: bevilja åtkomst till ett ASP.NET Core webb-API från en app med en enda sida'
titleSuffix: Azure AD B2C
description: 'I den här självstudien får du lära dig hur du använder Active Directory B2C för att skydda ett .NET Core webb-API och anropa API: et från ett enda-Page Node. js-program.'
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e5ced0c3e4ad05c33866548acb09189e03ed22f4
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949712"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Självstudie: bevilja åtkomst till ett ASP.NET Core webb-API från ett program med en sida med hjälp av Azure Active Directory B2C

Den här självstudien visar hur du anropar en Azure Active Directory B2C (Azure AD B2C)-skyddad ASP.NET Core webb-API-resurs från ett program på en sida.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Konfigurera exemplet för att använda programmet

## <a name="prerequisites"></a>Krav

* Slutför stegen och kraven i [Självstudier: aktivera autentisering i ett program med en enda sida med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 eller senare, eller Visual Studio Code
* .NET Core 2,2 eller senare
* Node.js

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du både Läs-och Skriv behörighet för webb-API: et.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registrera värdet under **omfattningar** för `demo.read` omfång som ska användas i ett senare steg när du konfigurerar ett program med en enda sida. Det fullständiga värdet för omfattning liknar `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett annat program måste du ge den program behörighet till webb-API: et.

I den nödvändiga självstudien har du skapat ett webb program med namnet *webapp1*. I den här självstudien konfigurerar du programmet för att anropa det webb-API som du skapade i ett tidigare avsnitt, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Webb programmet med en sida är registrerat för att anropa det skyddade webb-API: et. En användare autentiseras med Azure AD B2C för att använda ett program med en sida. En app med en enda sida får en auktorisering från Azure AD B2C för åtkomst till det skyddade webb-API: et.

## <a name="configure-the-sample"></a>Konfigurera exemplet

När webb-API:t är registrerat och har ett definierat omfång måste du konfigurera webb-API-koden så att den använder din Azure AD B2C-klientorganisation. I den här självstudien konfigurerar du ett exempel på ett .NET Core-webbprogram som du hämtar från GitHub.

[Hämta ett \*. zip-arkiv](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) eller klona exempel webb-API-projektet från GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurera webb-API

1. Öppna filen <em>B2C-WebAPI/**appSettings. JSON**</em>  i Visual Studio eller Visual Studio Code.
1. Ändra `AzureAdB2C` blocket så att det återspeglar ditt klient namn, program-ID för webb-API-programmet, namnet på din registrerings-/inloggnings princip och de omfattningar som du definierade tidigare. Blocket bör se ut ungefär som i följande exempel (med lämpliga `Tenant` och `ClientId` värden):

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

Du måste aktivera [CORS](https://docs.microsoft.com/aspnet/core/security/cors) i webb-API: et om du vill tillåta att ett program med en enda sida anropar ASP.net Core webb-API.

1. Lägg till CORS till metoden `ConfigureServices()` i *Startup.cs*.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Ange också `jwtOptions.Authority`-värdet till följande token för token-utfärdare i `ConfigureServices()`-metoden.

    Ersätt `<your-tenant-name>` med namnet på din B2C-klient.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. Konfigurera CORS i metoden `Configure()`.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Endast Visual Studio) Öppna filen *launchSettings. JSON* under **Egenskaper** i Solution Explorer och leta upp `iisExpress` blocket.
1. (Endast Visual Studio) Uppdatera `applicationURL`-värdet med det port nummer du angav när du registrerade *webapi1* -programmet i ett tidigare steg. Exempel:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Konfigurera ett program med en sida

En Enkels Ides applikation (SPA) från [föregående självstudie](active-directory-b2c-tutorials-spa.md) i serien använder Azure AD B2C för användarens registrering och inloggning, och anropar det ASP.net Core webb-API som skyddas av *frabrikamb2c* demo-klienten.

I det här avsnittet ska du uppdatera det enkla programmet för att anropa det ASP.NET Core webb-API som skyddas av *din* Azure AD B2C klient och som du kör på den lokala datorn.

Ändra inställningarna i SPA:

1. Öppna filen *index. html* i [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] -projektet som du laddade ned eller klonade i föregående självstudie.
1. Konfigurera exemplet med URI: n för *demonstrationen. Läs* omfattning som du skapade tidigare och URL: en för webb-API: et.
    1. I `appConfig` definition ersätter du `b2cScopes`-värdet med den fullständiga URI: n för omfånget (det **omfattnings** värde som du registrerade tidigare).
    1. Ändra `webApi` värde till den omdirigerings-URI som du lade till när du registrerade webb-API-programmet i ett tidigare steg.

    `appConfig` definitionen bör likna följande kodblock (med ditt klient namn i `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Köra SPA-och webb-API: et

Slutligen kör du både webb-API: et för ASP.NET Core och Node. js-programmet på den lokala datorn. Sedan loggar du in på ett program med en enda sida och trycker på en knapp för att initiera en begäran till det skyddade API: et.

Även om båda programmen körs lokalt i den här självstudien använder de Azure AD B2C för säker registrering/inloggning och för att bevilja åtkomst till det skyddade webb-API: et.

### <a name="run-the-aspnet-core-web-api"></a>Kör ASP.NET Core webb-API

I Visual Studio trycker du på **F5** för att bygga och felsöka lösningen *WebAPI. SLN* . När projektet startas visas en webb sida i din standard webbläsare som visar att webb-API: et är tillgängligt för förfrågningar.

Om du föredrar att använda `dotnet` CLI i stället för Visual Studio:

1. Öppna ett konsol fönster och ändra till den katalog som innehåller filen *\*. CSPROJ* . Exempel:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Skapa och kör webb-API genom att köra `dotnet run`.

    När API: et är igång bör du se utdata som liknar följande (för självstudien kan du ignorera eventuella `NETSDK1059` varningar):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Kör ensidesappen

1. Öppna ett konsol fönster och ändra till den katalog som innehåller Node. js-exemplet. Exempel:

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

1. Navigera till `http://localhost:6420` i webbläsaren om du vill visa programmet.
1. Logga in med e-postadressen och lösen ordet som du använde i [föregående självstudie](active-directory-b2c-tutorials-spa.md). Vid lyckad inloggning bör du se `User 'Your Username' logged-in`-meddelandet.
1. Klicka på knappen **anropa webb-API** . SPA får ett bemyndigande från Azure AD B2C och ansluter sedan till det skyddade webb-API: et för att visa innehållet på index sidan:

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

Nu när du har sett en SPA-begäran en resurs från ett skyddat webb-API, får du en djupare förståelse för hur dessa program typer interagerar med varandra och med Azure AD B2C.

> [!div class="nextstepaction"]
> [Program typer som kan användas i Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
