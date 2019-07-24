---
title: Självstudie – bevilja åtkomst till ett ASP.NET Core webb-API från ett program med en sida – Azure Active Directory B2C
description: 'Lär dig hur du använder Active Directory B2C för att skydda ett .NET Core webb-API och anropa API: et från ett Node. js-program med en enda sida.'
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b53ce30f4c49580bcd8ad3e259adf0300d8bd4a6
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369321"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Självstudier: Bevilja åtkomst till ett ASP.NET Core webb-API från en ensidesapp med hjälp av Azure Active Directory B2C

Den här självstudien visar hur du anropar en Azure Active Directory (Azure AD) B2C-skyddad ASP.NET Core webb-API-resurs från ett enda webb program.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Tilldela behörigheter till webb-API:et
> * Konfigurera exemplet för att använda programmet

## <a name="prerequisites"></a>Förutsättningar

* Slutför stegen och förutsättningarna i [Självstudie: Aktivera autentisering i ett program med en enda sida med](active-directory-b2c-tutorials-spa.md)hjälp av Azure Active Directory B2C.
* Visual Studio 2019 eller senare, eller Visual Studio Code
* .NET Core 2,2 eller senare
* Node.js

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en begäran från en skyddad resurs från klientprogram som använder en åtkomsttoken.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
1. Ange ett namn på programmet. Till exempel *webapi1*.
1. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
1. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `https://localhost:5000`.
1. För **app-ID-URI**anger du en API-slutpunkt-ID till den URI som visas. I självstudien anger `api`du, så att hela URI: n `https://contosotenant.onmicrosoft.com/api`liknar.
1. Klicka på **Skapa**.
1. Välj *webapi1* -programmet för att öppna dess egenskaps sida.
1. Registrera det **program-ID** som visas på sidan Egenskaper. Du behöver det här ID: t i ett senare steg när du konfigurerar webb programmet.

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du både Läs-och Skriv behörighet för webb-API: et.

1. Välj **program**och välj sedan *webapi1* för att öppna dess egenskaps sida om den inte redan är öppen.
1. Välj **Publicerade omfång**.
1. För **omfattning**, ange `Hello.Read`och för **Beskrivning**anger `Read access to hello`du.
1. För **omfattning**, ange `Hello.Write`och för **Beskrivning**anger `Write access to hello`du.
1. Välj **Spara**.
1. Registrera det **fullständiga värdet för omfattning** för `Hello.Read` det omfång som ska användas i ett senare steg när du konfigurerar ett program med en sida. Det fullständiga värdet för `https://yourtenant.onmicrosoft.com/api/Hello.Read`omfattning liknar.

De publicerade omfången kan användas för att tilldela behörighet för webb-API till ett klientprogram.

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett annat program måste du ge den program behörighet till webb-API: et.

I den nödvändiga självstudien har du skapat ett webb program med namnet *webapp1*. I den här självstudien konfigurerar du programmet för att anropa det webb-API som du skapade i ett tidigare avsnitt, *webapi1*.

1. Navigera till din B2C-klient i Azure Portal
1. Välj **Program** och därefter *webapp1*.
1. Välj **API-åtkomst** och därefter **Lägg till**.
1. I listrutan **Välj API** väljer du *webapi1*.
1. I listrutan **Välj reservationsomfång** väljer du omfången **Hello.Read** och **Hello.Write** som du definierade tidigare.
1. Klicka på **OK**.

Webb programmet med en sida är registrerat för att anropa det skyddade webb-API: et. En användare autentiseras med Azure AD B2C för att använda ett program med en sida. En app med en enda sida får en auktorisering från Azure AD B2C för åtkomst till det skyddade webb-API: et.

## <a name="configure-the-sample"></a>Konfigurera exemplet

När webb-API:t är registrerat och har ett definierat omfång måste du konfigurera webb-API-koden så att den använder din Azure AD B2C-klientorganisation. I den här självstudien konfigurerar du ett exempel på ett .NET Core-webbprogram som du hämtar från GitHub.

[Hämta ett \*zip-arkiv](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) eller klona exempel webb-API-projektet från GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurera webb-API

1. Öppna filen *B2C-WebAPI/* * appSettings. JSON** * i Visual Studio eller Visual Studio Code.
1. `AzureAdB2C` Ändra blocket så att det återspeglar ditt klient namn, program-ID: t för webb-API-programmet, namnet på din registrerings-och inloggnings princip och de omfattningar som du definierade tidigare. Blocket bör se ut ungefär som i följande exempel (med `Tenant` lämpliga `ClientId` värden och värden):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "Hello.Read",
      "ScopeWrite": "Hello.Write"
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

1. I `ConfigureServices()` -metoden `jwtOptions.Authority` ställer du in värdet på följande URI för token-utfärdare.

    Ersätt `<your-tenant-name>` med namnet på din B2C-klient.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. Konfigurera CORS i- metoden.`Configure()`

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Endast Visual Studio) Under **Egenskaper** i Solution Explorer öppnar du filen *launchSettings. JSON* och letar `iisExpress` sedan upp blocket.
1. (Endast Visual Studio) Uppdatera värdet med det port nummer du angav när du registrerade webapi1-programmet i ett tidigare steg.  `applicationURL` Exempel:

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
1. Konfigurera exemplet med URI för den *Hello. Read* -omfattning som du skapade tidigare och URL: en för webb-API: et.
    1. I definitionen ersätter du värdet med den fullständiga URI: n för omfattningen (det **fullständiga värdet** som du registrerade tidigare). `b2cScopes` `appConfig`
    1. Ändra värdet till det `applicationURL` värde som du angav i föregående avsnitt. `webApi`

    Definitionen bör likna följande kodblock (med ditt klient namn i stället för `<your-tenant-name>`): `appConfig`

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/Hello.Read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Köra SPA-och webb-API: et

Slutligen kör du både webb-API: et för ASP.NET Core och Node. js-programmet på den lokala datorn. Sedan loggar du in på ett program med en enda sida och trycker på en knapp för att initiera en begäran till det skyddade API: et.

Även om båda programmen körs lokalt i den här självstudien använder de Azure AD B2C för säker registrering/inloggning och för att bevilja åtkomst till det skyddade webb-API: et.

### <a name="run-the-aspnet-core-web-api"></a>Kör ASP.NET Core webb-API

I Visual Studio trycker du på **F5** för att bygga och felsöka lösningen *WebAPI. SLN* . När projektet startas visas en webb sida i din standard webbläsare som visar att webb-API: et är tillgängligt för förfrågningar.

Om du föredrar att använda `dotnet` cli i stället för Visual Studio:

1. Öppna ett konsol fönster och ändra till den katalog som innehåller  *\*. CSPROJ* -filen. Exempel:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Skapa och kör webb-API: et genom `dotnet run`att köra.

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
1. Logga in med e-postadressen och lösen ordet som du använde i [föregående självstudie](active-directory-b2c-tutorials-spa.md). När inloggningen är klar bör du se `User 'Your Username' logged-in` meddelandet.
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
> * Tilldela behörigheter till webb-API:et
> * Konfigurera exemplet för att använda programmet

Nu när du har sett en SPA-begäran en resurs från ett skyddat webb-API, får du en djupare förståelse för hur dessa program typer interagerar med varandra och med Azure AD B2C.

> [!div class="nextstepaction"]
> [Program typer som kan användas i Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
