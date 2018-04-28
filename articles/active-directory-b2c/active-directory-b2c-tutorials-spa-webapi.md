---
title: Självstudie – Bevilja åtkomst till ett ASP.NET Core webb-API från en ensidesapp med Azure Active Directory B2C | Microsoft Docs
description: Självstudie som visar hur du använder Active Directory B2C för att skydda ett .NET Core webb-API och anropar det från en ensidesapp.
services: active-directory-b2c
author: davidmu1
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: b1731a16dd2389f19d25a2594fe25a97b6ddd9f1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Självstudier: Bevilja åtkomst till ett ASP.NET Core webb-API från en ensidesapp med Azure Active Directory B2C

Den här självstudien lär dig anropa en ASP.NET Core webb-API-resurs som skyddas av Azure Active Directory (Azure AD) B2C från ensidesapp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Registrera ett webb-API i en Azure AD B2C-klientorganisation
> * Definiera och konfigurera omfång för ett webb-API
> * Tilldela behörighet till webb-API
> * Uppdatera exempelkod och använder Azure AD B2C för att skydda ett webb-API

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Slutför [självstudien för autentisering av användare med Azure Active Directory B2C i en ensidesapp](active-directory-b2c-tutorials-spa.md).
* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen **ASP.NET och webbutveckling**.
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) eller senare
* Installera [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registrera webb-API

Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en [begäran från en skyddad resurs](../active-directory/develop/active-directory-dev-glossary.md#resource-server) från [klientprogram](../active-directory/develop/active-directory-dev-glossary.md#client-application) som använder en [åtkomsttoken](../active-directory/develop/active-directory-dev-glossary.md#access-token) från Azure Active Directory. Registrering skapar [programmet och tjänstens huvudnamnsobjekt](../active-directory/develop/active-directory-dev-glossary.md#application-object) i klientorganisationen. 

Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Välj **Azure AD B2C** i listan över tjänster i Azure Portal.

2. I B2C-inställningarna klickar du på **Program** och sedan på **Lägg till**.

    Registrera webb-API-exemplet i klientorganisationen med följande inställningar.
    
    ![Lägga till ett nytt API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | Hello Core API | Ange ett **Namn** som beskriver ditt webb-API för utvecklare. |
    | **Ta med webbapp/webb-API** | Ja | Välj **Ja** om det är ett webb-API. |
    | **Tillåt implicit flöde** | Ja | Välj **Ja** eftersom API:et använder [OpenID Connect-inloggning](active-directory-b2c-reference-oidc.md). |
    | **Svarswebbadress** | `http://localhost:44332` | Svarswebbadresser är slutpunkter där Azure AD B2C returnerar de token som API:et begär. I den här självstudien körs webb-API-exemplet lokalt (localhost) och lyssnar på port 5000. |
    | **URI för app-id** | HelloCoreAPI | URI:n identifierar API:et i klientorganisationen. Det gör att flera API:er kan registreras per klientorganisation. [Omfång](../active-directory/develop/active-directory-dev-glossary.md#scopes) styr åtkomsten till den skyddade API-resursen och definieras med URI:n för app-ID. |
    | **Inbyggd klient** | Nej | Eftersom det här är ett webb-API och inte en intern klient väljer du Nej. |
    
3. Klicka på **Skapa** för att registrera API:et.

Registrerade API visas i programlistan för Azure AD B2C-klientorganisationen. Välj webb-API i listan. Webb-API:ets egenskapsruta visas.

![Egenskaper för webb-API](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Anteckna det **Programklients-id** som visas. Detta ID identifierar API:et och behövs när API:et konfigureras senare under självstudierna.

När webb-API:et registreras i Azure AD B2C skapas ett förtroende. Eftersom API:et är registrerat med B2C kan API:et nu lita på de B2C-åtkomsttoken det får från andra program.

## <a name="define-and-configure-scopes"></a>Definiera och konfigurera omfång

[Omfång](../active-directory/develop/active-directory-dev-glossary.md#scopes) är ett sätt att styra åtkomst till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du läs- och skrivrättigheter för webb-API:et.

### <a name="define-scopes-for-the-web-api"></a>Definiera omfång för webb-API

Registrerade API visas i programlistan för Azure AD B2C-klientorganisationen. Välj webb-API i listan. Webb-API:ets egenskapsruta visas.

Klicka på **Publicerade områden (förhandsgranskning)**.

Konfigurera omfång för API:t genom att lägga till följande poster. 

![omfång definierade i webb-api](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Inställning      | Föreslaget värde  | Beskrivning                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Omfång** | demo.read | Läsåtkomst till demo-API |

Klicka på **Spara**.

De publicerade omfången kan användas för att tilldela behörighet för webb-API till ett klientprogram.

### <a name="grant-app-permissions-to-web-api"></a>Tilldela appbehörighet till webb-API

Om du vill anropa ett skyddat webb-API från en app måste du ge appen åtkomst till API:t. I den här kursen används ensidesappen som skapades i [självstudien för autentisering av användare med Azure Active Directory B2C i en ensidesapp (JavaScript)](active-directory-b2c-tutorials-spa.md).

1. I Azure Portal väljer du **Azure AD B2C** från listan med tjänster och klickar på **Program** för att visa den registrerade applistan.

2. Välj **My sample single page app** från applistan och klicka på **API-åtkomst (förhandsgranskning)** och sedan på **Lägg till**.

3. I listrutan **Välj API** väljer du det registrerade webb-API:et **Hello CoreAPI**.

4. I listrutan **Välj områden** väljer du de omfång du definierade vid registreringen av webb-API:t.

    ![välja omfång för app](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Klicka på **OK**.

**My sample single page app** är registrerad för att anropa det skyddade **Hello Core API:et**. En användare [autentiserar](../active-directory/develop/active-directory-dev-glossary.md#authentication) med Azure AD B2C för att använda WPF-skrivbordsappen. Skrivbordsappen får ett [auktoriseringsbeviljande](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) från Azure AD B2C som ger tillgång till det skyddade webb-API:et.

## <a name="update-code"></a>Uppdatera kod

När webb-API:et är registrerat och har ett definierat omfång måste du konfigurera webb-API-koden så den använder din Azure AD B2C-klientorganisation. I den här självstudien får du konfigurera en .NET Core webbapp som du kan ladda ned från GitHub. 

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) eller klona exempelwebbappen från GitHub.

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
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>Aktivera CORS

Om du vill tillåta ensidesappen att anropa ASP.NET Core webb-API:et, måste du aktivera [CORS](https://docs.microsoft.com/en-us/aspnet/core/security/cors).

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

### <a name="configure-the-single-page-app"></a>Konfigurera ensidesappen

Ensidesappen använder Azure AD B2C för användarregistrering, inloggning och skyddade ASP.NET Core webb-API-anrop. Du måste uppdatera ensidesappens anrop till .NET Core webb-API:et.
Så här ändrar du appinställningarna:

1. Öppna filen `index.html` i exemplet för ensidesappen i Node.js.
2. Konfigurera exemplet med registreringsinformation för Azure AD B2C-klientorganisationen. Ändra värdena för **b2cScopes** och **webApi** i följande kodrader:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:58553/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Kör SPA-appen och webb-API:et

Du behöver köra både Node.js-ensidesappen och .NET Core webb-API:et.

### <a name="run-the-aspnet-core-web-api"></a>Kör ASP.NET Core webb-API:et 

Tryck på **F5** för att felsöka **B2C-WebAPI.sln** i Visual Studio.

När projektet startar visas en webbsida i din standardwebbläsare som meddelar att webb-API:et är tillgängligt för förfrågningar.

### <a name="run-the-single-page-app"></a>Kör ensidesappen

1. Starta en kommandotolk för Node.js.
2. Ändra till den katalog som innehåller Node.js-exemplet. Till exempel `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Kör följande kommandon:
    ```
    npm install && npm update
    node server.js
    ```

    Konsolfönstret visar portnumret där appen finns.
    
    ```
    Listening on port 6420...
    ```

4. Använd en webbläsare för att gå till adressen `http://localhost:6420` för att visa appen.
5. Logga in med e-postadressen och lösenordet som skapades i [autentisering av användare med Azure Active Directory B2C i en ensidesapp (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Klicka på knappen **Anropa API**.

När du registrerar dig eller loggar in med ett användarkonto anropar exemplet det skyddade webb-API:et och returnerar ett resultat.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du kan skydda ett webb-API genom att registrera och definiera omfång i Azure AD B2C. Lär dig mer genom att titta på tillgängliga Azure AD B2C-kodexempel.

> [!div class="nextstepaction"]
> [Kodexempel för Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)