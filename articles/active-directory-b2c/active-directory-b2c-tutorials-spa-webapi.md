---
title: Självstudie – Bevilja åtkomst till ett ASP.NET Core webb-API från en ensidesapp med Azure Active Directory B2C | Microsoft Docs
description: Självstudie som visar hur du använder Active Directory B2C för att skydda ett .NET Core webb-API och anropar det från en ensidesapp.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 13cbf1e81e0d203c181efb0881ec2a437cbaef24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315668"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Självstudier: Bevilja åtkomst till ett ASP.NET Core webb-API från en ensidesapp med hjälp av Azure Active Directory B2C

I den här självstudien får du lära dig att anropa en ASP.NET Core webb-API-resurs som skyddas av Azure Active Directory (Azure AD) B2C från en ensidesapp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Tilldela behörigheter till webb-API:et
> * Konfigurera exemplet för att använda programmet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför stegen och kraven i [självstudien: Aktivera autentisering för en ensidesapp med konton med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en begäran från en skyddad resurs från klientprogram som använder en åtkomsttoken.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn på programmet. Till exempel *webapi1*.
6. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
7. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `https://localhost:5000`.
8. För **URI för app-ID** anger du den identifierare som används för webb-API:t. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du program-ID:t du kommer att använda när du konfigurerar webbappen.

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du läs- och skrivrättigheter för webb-API:et.

1. Välj **Program** och sedan *webapi1*.
2. Välj **Publicerade omfång**.
3. Som **omfång** anger du `Hello.Read` och som beskrivning anger du `Read access to hello`.
4. Som **omfång** anger du `Hello.Write` och som beskrivning anger du `Write access to hello`.
5. Klicka på **Spara**.

De publicerade omfången kan användas för att tilldela behörighet för webb-API till ett klientprogram.

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:et. I den obligatoriska föregående självstudien skapade du en webbapp i Azure AD B2C med namnet *webapp1*. Du använder det programmet för att anropa webb-API:et.

1. Välj **Program** och därefter *webapp1*.
2. Välj **API-åtkomst** och därefter **Lägg till**.
3. I listrutan **Välj API** väljer du *webapi1*.
4. I listrutan **Välj reservationsomfång** väljer du omfången **Hello.Read** och **Hello.Write** som du definierade tidigare.
5. Klicka på **OK**.

**My sample single page app** är registrerad för att anropa det skyddade **Hello Core API:et**. En användare autentiserar med Azure AD B2C för att använda ensidesappen. Ensidesappen får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:et.

## <a name="configure-the-sample"></a>Konfigurera exemplet

När webb-API:et är registrerat och har ett definierat omfång måste du konfigurera webb-API-koden så den använder din Azure AD B2C-klientorganisation. I den här självstudien får du konfigurera ett .NET Core-program som du kan ladda ned från GitHub. [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) eller klona exempelwebbappen från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurera webb-API

1. Öppna **B2C-WebAPI.sln**-lösningen i Visual Studio.
2. Öppna filen **appsettings.json**. Uppdatera följande värden för att konfigurera webb-API:et för att använda din klient:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Aktivera CORS

Om du vill tillåta ensidesappen att anropa ASP.NET Core webb-API:et måste du aktivera [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. Lägg till CORS till metoden `ConfigureServices()` i **Startup.cs**.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. Konfigurera CORS i metoden `Configure()` i **Startup.cs**.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Öppna filen **launchSettings.json** under **egenskaper**, leta upp inställningen **iisSettings** *applicationURL* och ange porten som registrerats för API för svars-URL`http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Konfigurera ensidesappen

Ensidesappen använder Azure AD B2C för användarregistrering, inloggning och skyddade ASP.NET Core webb-API-anrop. Du måste uppdatera ensidesappen för att anropa .NET Core webb-API:et.

Så här ändrar du appinställningarna:

1. Öppna filen `index.html`.
2. Konfigurera exemplet med registreringsinformation för Azure AD B2C-klientorganisationen. I följande kod lägger du till namnet på din klientorganisation i **b2cScopes** och ändrar **webApi**-värdet till det *applicationURL*-värde som du skrev ned tidigare:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Köra SPA-programmet och webb-API:et

Du behöver köra både Node.js-ensidesappen och .NET Core webb-API:et.

### <a name="run-the-aspnet-core-web-api"></a>Kör ASP.NET Core webb-API:et 

Tryck på **F5** för att felsöka **B2C-WebAPI.sln** i Visual Studio.

När projektet startar visas en webbsida i din standardwebbläsare som meddelar att webb-API:et är tillgängligt för förfrågningar.

### <a name="run-the-single-page-app"></a>Kör ensidesappen

1. Starta en kommandotolk för Node.js.
2. Gå till den katalog som innehåller Node.js-exemplet. Till exempel `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Kör följande kommandon:
    ```
    npm install && npm update
    node server.js
    ```

    Konsolfönstret visar portnumret där programmet finns.
    
    ```
    Listening on port 6420...
    ```

4. Använd en webbläsare för att gå till adressen `http://localhost:6420` för att visa programmet.
5. Logga in med e-postadressen och lösenordet som skapades i [autentisering av användare med Azure Active Directory B2C i en ensidesapp (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Klicka på **Anropa API**.

När du registrerar dig eller loggar in med ett användarkonto anropar exemplet det skyddade webb-API:et och returnerar ett resultat.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Tilldela behörigheter till webb-API:et
> * Konfigurera exemplet för att använda programmet

> [!div class="nextstepaction"]
> [Självstudie: Lägga till identitetsproviders i program i Azure Active Directory B2C](tutorial-add-identity-providers.md)
