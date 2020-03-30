---
title: Arbeta med .NET-server för server för server för server för server som är server som är server för server som är server som är server
description: Lär dig hur du arbetar med .NET-server för server för server för server för server för Azure App Service Mobile Apps.
keywords: apptjänst, azure app-tjänst, mobilapp, mobiltjänst, skala, skalbar, appdistribution, azure appdistribution
author: conceptdev
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: cafb0a7e2bf0fbce82448236a2da98079144121e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250145"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Arbeta med .NET-server för server för server för server för server för server för azure mobile apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Det här avsnittet visar hur du använder .NET-server för server för server för server SDK i viktiga Azure App Service Mobile Apps-scenarier. Azure Mobile Apps SDK hjälper dig att arbeta med mobila klienter från ditt ASP.NET program.

> [!TIP]
> [.NET-servern SDK för Azure Mobile Apps][2] är öppen källkod på GitHub. Databasen innehåller all källkod, inklusive hela server-SDK-enhetstestsviten och några exempelprojekt.
>
>

## <a name="reference-documentation"></a>Referensdokumentation
Referensdokumentationen för servern SDK finns här: [Azure Mobile Apps .NET Reference][1].

## <a name="how-to-create-a-net-mobile-app-backend"></a><a name="create-app"></a>Så här skapar du en .NET Mobile App-backend
Om du startar ett nytt projekt kan du skapa ett App Service-program med antingen [Azure-portalen] eller Visual Studio. Du kan köra App Service-programmet lokalt eller publicera projektet i din molnbaserade App Service-mobilapp.

Om du lägger till mobila funktioner i ett befintligt projekt läser du [avsnittet Hämta och initiera SDK.](#install-sdk)

### <a name="create-a-net-backend-using-the-azure-portal"></a>Skapa en .NET-backend med Azure-portalen
Så här skapar du en mobile backend för App Service följer du [snabbstartshandledningen][3] eller så här:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Tillbaka i bladet *Kom igång* under Skapa **ett tabell-API**väljer du **C#** som **serverningsspråk**. Klicka på **Hämta,** extrahera de komprimerade projektfilerna till den lokala datorn och öppna lösningen i Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Skapa en .NET-backend med Visual Studio 2017

Installera Azure-arbetsbelastningen via Visual Studio Installer för att publicera till Azure Mobile Apps-projektet från Visual Studio. När du har installerat SDK skapar du ett ASP.NET program med hjälp av följande steg:

1. Öppna dialogrutan **Nytt projekt** (från **Arkiv** > **nytt** > **projekt...**).
2. Expandera **Visual C#** och välj **Webb**.
3. Välj **ASP.NET webbprogram (.NET Framework)**.
4. Fyll i projektnamnet. Klicka sedan på **OK**.
5. Välj **Azure Mobile App** i listan över mallar.
6. Klicka på **OK** för att skapa lösningen.
7. Högerklicka på projektet i **Lösningsutforskaren** och välj **Publicera...** och välj sedan **App Service** som publiceringsmål.
8. Följ anvisningarna för att autentisera och välja en ny eller befintlig Azure App-tjänst att publicera.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Skapa en .NET-backend med Visual Studio 2015

Installera [Azure SDK för .NET][4] (version 2.9.0 eller senare) för att skapa ett Azure Mobile Apps-projekt i Visual Studio. När du har installerat SDK skapar du ett ASP.NET program med hjälp av följande steg:

1. Öppna dialogrutan **Nytt projekt** (från **Arkiv** > **nytt** > **projekt...**).
2. Expandera **Mallar** > **Visual C#** och välj **Webb**.
3. Välj **ASP.NET-webbapp**.
4. Fyll i projektnamnet. Klicka sedan på **OK**.
5. Välj **Azure Mobile App**under ASP.NET *4.5.2-mallar*. Kontrollera **värdvärden i molnet** för att skapa en mobil backend i molnet som du kan publicera det här projektet till.
6. Klicka på **OK**.

## <a name="how-to-download-and-initialize-the-sdk"></a><a name="install-sdk"></a>Så här: Ladda ner och initiera SDK
SDK finns på [NuGet.org]. Det här paketet innehåller de basfunktioner som krävs för att komma igång med SDK. Om du vill initiera SDK måste du utföra åtgärder på **HttpConfiguration-objektet.**

### <a name="install-the-sdk"></a>Installera SDK:n
Om du vill installera SDK högerklickar du på serverprojektet i Visual Studio, väljer **Hantera NuGet-paket**, söker efter [Paketet Microsoft.Azure.Mobile.Server] och klickar sedan på **Installera**.

### <a name="initialize-the-server-project"></a><a name="server-project-setup"></a>Initiera serverprojektet
Ett .NET-serverprojekt initieras på samma sätt som andra ASP.NET projekt, genom att inkludera en OWIN-startklass. Se till att du har `Microsoft.Owin.Host.SystemWeb`refererat till NuGet-paketet . Om du vill lägga till den här klassen i Visual Studio högerklickar du på serverprojektet och väljer **Lägg till** >
**nytt objekt**och sedan **webbgeneral** > **General** > **OWIN-startklass**.  En klass genereras med följande attribut:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

I `Configuration()` metoden för din OWIN-startklass använder du ett **HttpConfiguration-objekt** för att konfigurera Azure Mobile Apps-miljön.
I följande exempel initieras serverprojektet utan några tillagda funktioner:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Om du vill aktivera enskilda funktioner måste du anropa tilläggsmetoder på **MobileAppConfiguration-objektet** innan du anropar **ApplyTo**. Följande kod lägger till till exempel standardvägarna till alla `[MobileAppController]` API-styrenheter som har attributet under initieringen:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Snabbstarten för servern från Azure-portalen **anropar UseDefaultConfiguration()**. Detta motsvarar följande inställningar:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

De förlängningsmetoder som används är:

* `AddMobileAppHomeController()`tillhandahåller standardsidan för Azure Mobile Apps.
* `MapApiControllers()`tillhandahåller anpassade API-funktioner för WebAPI-styrenheter dekorerade med `[MobileAppController]` attributet.
* `AddTables()`tillhandahåller en mappning av slutpunkterna `/tables` till tabellstyrenheter.
* `AddTablesWithEntityFramework()`är en kort hand `/tables` för att mappa slutpunkterna med hjälp av Entity Framework-baserade styrenheter.
* `AddPushNotifications()`tillhandahåller en enkel metod för att registrera enheter för Notification Hubs.
* `MapLegacyCrossDomainController()`tillhandahåller standard-CORS-huvuden för lokal utveckling.

### <a name="sdk-extensions"></a>SDK-tillägg
Följande NuGet-baserade tilläggspaket innehåller olika mobila funktioner som kan användas av ditt program. Du aktiverar tillägg under initieringen med hjälp av **MobileAppConfiguration-objektet.**

* [Microsoft.Azure.Mobile.Server.Quickstart] stöder den grundläggande konfigurationen av mobilappar. Lade till konfigurationen genom att anropa metoden **UseDefaultConfiguration** extension under initieringen. Det här tillägget innehåller följande tillägg: Meddelanden, autentisering, entitet, Tabeller, Korsdomän och Hempaket. Det här paketet används av Snabbstarten för mobilappar som är tillgänglig på Azure-portalen.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementerar standardsidan som *den här mobilappen är igång* för webbplatsroten. Lägg till i konfigurationen genom att anropa tilläggsmetoden **AddMobileAppHomeController.**
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) innehåller klasser för att arbeta med data och uppsättningar upp datapipelinen. Lägg till i konfigurationen genom att anropa tilläggsmetoden **AddTables.**
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) gör det möjligt för entitetsramverket att komma åt data i SQL-databasen. Lägg till i konfigurationen genom att anropa tilläggsmetoden **AddTablesWithEntityFramework.**
* [Microsoft.Azure.Mobile.Server.Authentication] Aktiverar autentisering och konfigurerar OWIN-mellanprogram som används för att validera token. Lägg till i konfigurationen genom att anropa **AddAppServiceAuthentication** och **IAppBuilder**. **AnvändTilläggsmetoder för Tillägg för Användningstjänstautentisering.**
* [Microsoft.Azure.Mobile.Server.Notifications] Aktiverar push-meddelanden och definierar en slutpunkt för push-registrering. Lägg till i konfigurationen genom att anropa **tilläggsmetoden AddPushNotifications.**
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Skapar en styrenhet som betjänar data till äldre webbläsare från mobilappen. Lägg till i konfigurationen genom att anropa metoden **MapLegacyCrossDomainController.**
* [Microsoft.Azure.Mobile.Server.Login] Tillhandahåller metoden AppServiceLoginHandler.CreateToken(), som är en statisk metod som används under anpassade autentiseringsscenarier.

## <a name="how-to-publish-the-server-project"></a><a name="publish-server-project"></a>Så här publicerar du serverprojektet
I det här avsnittet visas hur du publicerar tillbakabebyggt .NET-backend-projekt från Visual Studio. Du kan också distribuera backend-projektet med [Git](../app-service/deploy-local-git.md) eller någon av de andra metoder som finns där.

1. I Visual Studio återskapar du projektet för att återställa NuGet-paket.
2. Högerklicka på projektet i Solution Explorer och klicka på **Publicera**. Första gången du publicerar måste du definiera en publiceringsprofil. När du redan har en profil definierad kan du markera den och klicka på **Publicera**.
3. Om du uppmanas att välja ett publiceringsmål klickar du på **Microsoft Azure App Service** > **Next**och loggar sedan (om det behövs) in med dina Azure-autentiseringsuppgifter.
   Visual Studio hämtar och lagrar dina publiceringsinställningar direkt från Azure på ett säkert sätt.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Välj **prenumeration,** välj **Resurstyp** från **Visa,** expandera **Mobilapp**och klicka på din mobile app-backend och klicka sedan på **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Verifiera publiceringsprofilinformationen och klicka på **Publicera**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    När din mobile app-backend har publicerats visas en målsida som visar framgång.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="how-to-define-a-table-controller"></a><a name="define-table-controller"></a>Så här definierar du en tabellkontrollant
Definiera en tabellstyrenhet för att exponera en SQL-tabell för mobila klienter.  För att konfigurera en tabellkontrollant krävs tre steg:

1. Skapa en DTO-klass (Data Transfer Object).
2. Konfigurera en tabellreferens i klassen Mobile DbContext.
3. Skapa en tabellstyrenhet.

Ett DTO (Data Transfer Object) är ett vanligt `EntityData`C#-objekt som ärver från .  Ett exempel:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO används för att definiera tabellen i SQL-databasen.  Om du vill skapa `DbSet<>` databasposten lägger du till en egenskap i den DbContext som du använder.  I standardprojektmallen för Azure Mobile Apps kallas `Models\MobileServiceContext.cs`DbContext :

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Om du har Azure SDK installerat kan du nu skapa en malltabellkontrollant enligt följande:

1. Högerklicka på mappen Controllers och välj **Lägg till** > **controller...**.
2. Välj alternativet **Tabellkontrollant för Azure Mobile Apps** och klicka sedan på Lägg **till**.
3. I dialogrutan **Lägg till handkontroll:**
   * Välj din nya DTO i listrutan **Modellklass.**
   * Välj klassen Mobile Service DbContext i listrutan **DbContext.**
   * Controller-namnet skapas åt dig.
4. Klicka på **Lägg till**.

Snabbstartsserverprojektet innehåller ett exempel på en enkel **TodoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a><a name="adjust-pagesize"></a>Så här justerar du tabellväxlingsstorleken
Som standard returnerar Azure Mobile Apps 50 poster per begäran.  Växling säkerställer att klienten inte binder upp sin gränssnittstråd eller servern för länge, vilket garanterar en bra användarupplevelse. Om du vill ändra tabellväxlingsstorleken ökar du serversidans "tillåtna frågestorlek" och sidstorleken på `EnableQuery` klientsidan Serversidan "tillåten frågestorlek" justeras med attributet:

    [EnableQuery(PageSize = 500)]

Kontrollera att PageSize är samma eller större än den storlek som begärs av klienten.  Mer information om hur du ändrar klientsidans storlek finns i den specifika klientens HOWTO-dokumentation.

## <a name="how-to-define-a-custom-api-controller"></a>Så här definierar du en anpassad API-styrenhet
Den anpassade API-styrenheten ger den mest grundläggande funktionen för din mobile app-serverdel genom att exponera en slutpunkt. Du kan registrera en mobilspecifik API-styrenhet med attributet [MobileAppController]. Attributet `MobileAppController` registrerar rutten, ställer in JSON-serialiseraren För mobila appar och aktiverar [kontroll av klientversion](app-service-mobile-client-and-server-versioning.md).

1. Högerklicka på mappen Controllers i Visual Studio och klicka sedan på **Lägg till** > **styrenhet,** välj **Webb-API 2-styrenhet&mdash;Tom** och klicka på Lägg **till**.
2. Ange ett **controller-namn**, till exempel `CustomController`, och klicka på Lägg **till**.
3. Lägg till följande med uttrycket för den nya styrenheten:

        using Microsoft.Azure.Mobile.Server.Config;
4. Använd attributet **[MobileAppController]** på klassdefinitionen för API-styrenhet, som i följande exempel:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. I filen App_Start/Startup.MobileApp.cs lägger du till ett anrop till **mapapicontrollers-tilläggsmetoden,** som i följande exempel:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Du kan också `UseDefaultConfiguration()` använda tilläggsmetoden i stället för `MapApiControllers()`. Alla styrenheter som inte har **MobileAppControllerAttribute** tillämpas kan fortfarande nås av klienter, men det kanske inte förbrukas korrekt av klienter som använder någon Mobile App-klient SDK.

## <a name="how-to-work-with-authentication"></a>Så här: Arbeta med autentisering
Azure Mobile Apps använder App Service Autentisering / Auktorisering för att skydda din mobila backend.  I det här avsnittet visas hur du utför följande autentiseringsrelaterade uppgifter i serverprojektet för .NET-serverserver:

* [Så här lägger du till autentisering i ett serverprojekt](#add-auth)
* [Så här: Använd anpassad autentisering för ditt program](#custom-auth)
* [Så här hämtar du autentiserat användarinformation](#user-info)
* [Så här begränsar du dataåtkomst för behöriga användare](#authorize)

### <a name="how-to-add-authentication-to-a-server-project"></a><a name="add-auth"></a>Så här lägger du till autentisering i ett serverprojekt
Du kan lägga till autentisering i serverprojektet genom att utöka **MobileAppConfiguration-objektet** och konfigurera OWIN-mellanprogram. När du installerar [paketet Microsoft.Azure.Mobile.Server.Quickstart] och anropar metoden **UseDefaultConfiguration** kan du gå vidare till steg 3.

1. Installera [microsoft.Azure.Mobile.Server.Authentication-paketet] i Visual Studio.
2. Lägg till följande kodrad i början av **konfigurationsmetoden** i Startup.cs projektfil:

        app.UseAppServiceAuthentication(config);

    Den här OWIN middleware-komponenten validerar token som utfärdats av den associerade App Service-gatewayen.
3. Lägg `[Authorize]` till attributet i en styrenhet eller metod som kräver autentisering.

Mer information om hur du autentiserar klienter till din serverd i mobilappar finns i [Lägga till autentisering i appen](app-service-mobile-ios-get-started-users.md).

### <a name="how-to-use-custom-authentication-for-your-application"></a><a name="custom-auth"></a>Så här: Använd anpassad autentisering för ditt program
> [!IMPORTANT]
> För att aktivera anpassad autentisering måste du först aktivera App Service-autentisering utan att välja en leverantör för din App-tjänst i Azure-portalen. Detta aktiverar WEBSITE_AUTH_SIGNING_KEY miljövariabeln när den finns.
> 
> 
> Om du inte vill använda någon av apptjänstautentiserings-/auktoriseringsleverantörerna kan du implementera ditt eget inloggningssystem. Installera [microsoft.azure.mobile.server.login-paketet] för att hjälpa till med autentiseringstokengenerering.  Ange din egen kod för att validera användaruppgifter. Du kan till exempel kontrollera mot saltade och hashade lösenord i en databas. I exemplet nedan `isValidAssertion()` är metoden (definierad någon annanstans) ansvarig för dessa kontroller.

Den anpassade autentiseringen exponeras genom att `register` `login` skapa en ApiController och exponera och åtgärder. Klienten bör använda ett anpassat användargränssnitt för att samla in information från användaren.  Informationen skickas sedan till API:et med ett vanligt HTTP POST-anrop. När servern har validerat påståendet utfärdas en `AppServiceLoginHandler.CreateToken()` token med metoden.  ApiController **bör inte** `[MobileAppController]` använda attributet.

En `login` exempelåtgärd:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

I föregående exempel är LoginResult och LoginResultUser serializable-objekt som exponerar obligatoriska egenskaper. Klienten förväntar sig att inloggningssvar ska returneras som JSON-objekt i formuläret:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Metoden `AppServiceLoginHandler.CreateToken()` innehåller en *målgrupp* och en emittentparameter. *issuer* Båda dessa parametrar är inställda på URL:en för programroten med hjälp av HTTPS-schemat. På samma sätt bör du ange *secretKey* till värdet av programmets signeringsnyckel. Distribuera inte signeringsnyckeln i en klient eftersom den kan användas för att mint nycklar och personifiera användare. Du kan hämta signeringsnyckeln medan du finns i App Service genom att referera till miljövariabeln *FÖR WEBBPLATSAUTENTISERINGSSIGNERINGSNYCKEL.\_\_\_* Om det behövs i en lokal felsökningskontext följer du instruktionerna i avsnittet [Lokal felsökning med autentisering](#local-debug) för att hämta nyckeln och lagra den som en programinställning.

Den utfärdade token kan också innehålla andra anspråk och ett utgångsdatum.  Minimalt måste den utfärdade token innehålla ett ämnesanspråk **(under)** anspråk.

Du kan stödja `loginAsync()` standardklientmetoden genom att överbelasta autentiseringsvägen.  Om klienten `client.loginAsync('custom');` ringer för att logga `/.auth/login/custom`in måste rutten vara .  Du kan ange vägen för den `MapHttpRoute()`anpassade autentiseringsstyrenheten med:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Med `loginAsync()` hjälp av metoden säkerställer att autentiseringstoken är kopplad till varje efterföljande anrop till tjänsten.
>
>

### <a name="how-to-retrieve-authenticated-user-information"></a><a name="user-info"></a>Så här hämtar du autentiserat användarinformation
När en användare autentiseras av App Service kan du komma åt det tilldelade användar-ID:t och annan information i .NET-backend-koden. Användarinformationen kan användas för att fatta auktoriseringsbeslut i backend. Följande kod hämtar användar-ID:et som är kopplat till en begäran:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID härleds från det leverantörsspecifika användar-ID:t och är statiskt för en viss användare och inloggningsleverantör.  SID är null för ogiltiga autentiseringstoken.

Med App Service kan du också begära specifika anspråk från din inloggningsleverantör. Varje identitetsprovider kan tillhandahålla mer information med identitetsprovidern SDK.  Du kan till exempel använda Facebook Graph API för information om vänner.  Du kan ange anspråk som begärs i providerbladet i Azure-portalen. Vissa anspråk kräver ytterligare konfiguration med identitetsprovidern.

Följande kod anropar **metoden GetAppServiceIdentityAsync-tillägg** för att hämta inloggningsuppgifterna, som innehåller åtkomsttoken som behövs för att göra förfrågningar mot Facebook Graph API:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Lägg till en `System.Security.Principal` medsats för att tillhandahålla metoden **GetAppServiceIdentityIdentitysync-tillägg.**

### <a name="how-to-restrict-data-access-for-authorized-users"></a><a name="authorize"></a>Så här begränsar du dataåtkomst för behöriga användare
I föregående avsnitt visade vi hur du hämtar användar-ID för en autentrad användare. Du kan begränsa åtkomsten till data och andra resurser baserat på det här värdet. Att till exempel lägga till en userId-kolumn i tabeller och filtrera frågeresultaten efter användar-ID är ett enkelt sätt att begränsa returnerade data endast till behöriga användare. Följande kod returnerar endast datarader när SID matchar värdet i kolumnen UserId i tabellen TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Metoden `Query()` returnerar `IQueryable` en som kan manipuleras av LINQ för att hantera filtrering.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Så här lägger du till push-meddelanden i ett serverprojekt
Lägg till push-meddelanden i serverprojektet genom att utöka **MobileAppConfiguration-objektet** och skapa en Notification Hubs-klient.

1. Högerklicka på serverprojektet i Visual Studio och klicka på Hantera `Microsoft.Azure.Mobile.Server.Notifications` **NuGet-paket**, sök efter och klicka sedan på **Installera**.
2. Upprepa det här `Microsoft.Azure.NotificationHubs` steget för att installera paketet, som innehåller client-biblioteket Notification Hubs.
3. I App_Start/Startup.MobileApp.cs och lägg till ett anrop till tilläggsmetoden **AddPushNotifications()** under initieringen:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Lägg till följande kod som skapar en Notification Hubs-klient:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Du kan nu använda notification hubs-klienten för att skicka push-meddelanden till registrerade enheter. Mer information finns i [Lägg till push-meddelanden till din app](app-service-mobile-ios-get-started-push.md). Mer information om meddelandehubbar finns i [Översikt över meddelandehubbar](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="how-to-enable-targeted-push-using-tags"></a><a name="tags"></a>Så här aktiverar du riktad push med taggar
Med meddelandehubbar kan du skicka riktade meddelanden till specifika registreringar med hjälp av taggar. Flera taggar skapas automatiskt:

* Installations-ID identifierar en specifik enhet.
* Användar-ID:t baserat på det autentiserade SID identifierar en viss användare.

Installations-ID kan nås från **egenskapen installationId** på **MobileServiceClient**.  I följande exempel visas hur du använder ett installations-ID för att lägga till en tagg i en viss installation i Notification Hubs:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Alla taggar som tillhandahålls av klienten under push-meddelanderegistrering ignoreras av serverd när du skapar installationen. Om du vill att en klient ska kunna lägga till taggar i installationen måste du skapa ett anpassat API som lägger till taggar med föregående mönster.

Se [Klient-tillagda push-meddelandetaggar][5] i snabbstartsexemplet för apptjänst i mobiler.

## <a name="how-to-send-push-notifications-to-an-authenticated-user"></a><a name="push-user"></a>Så här skickar du push-meddelanden till en autentiserat användare
När en autentrad användare registrerar för push-meddelanden läggs en användar-ID-tagg automatiskt till i registreringen. Med den här taggen kan du skicka push-meddelanden till alla enheter som registrerats av den personen. Följande kod hämtar SID för användaren som gör begäran och skickar en mall push-meddelande till varje enhetsregistrering för den personen:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

När du registrerar dig för push-meddelanden från en autentrad klient kontrollerar du att autentiseringen är klar innan du försöker registrera dig. Mer information finns i [Push till användare][6] i snabbstartsprovet för App Service Mobile Apps för .NET-backend.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Felsöka och felsöka .NET Server SDK
Azure App Service innehåller flera felsöknings- och felsökningstekniker för ASP.NET program:

* [Övervaka en Azure App-tjänst](../app-service/web-sites-monitor.md)
* [Aktivera diagnostikloggning i Azure App-tjänsten](../app-service/troubleshoot-diagnostic-logs.md)
* [Felsöka en Azure App-tjänst i Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Loggning
Du kan skriva till App Service diagnostiska loggar med hjälp av standard ASP.NET spårningsskrivning. Innan du kan skriva till loggarna måste du aktivera diagnostik i din mobile app-backend.

Så här aktiverar du diagnostik och skriv till loggarna:

1. Följ stegen i [Aktivera programloggning (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows).
2. Lägg till följande med hjälp av utdrag i kodfilen:

        using System.Web.Http.Tracing;
3. Skapa en spårningsskrivare som ska skrivas från .NET-backend till diagnostikloggarna enligt följande:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Publicera om serverprojektet och öppna serverdelen för mobilappen för att köra kodsökvägen med loggningen.
5. Hämta och utvärdera loggarna enligt beskrivningen i [Access-loggfiler](../app-service/troubleshoot-diagnostic-logs.md#access-log-files).

### <a name="local-debugging-with-authentication"></a><a name="local-debug"></a>Lokal felsökning med autentisering
Du kan köra programmet lokalt för att testa ändringar innan du publicerar dem i molnet. För de flesta Azure Mobile Apps-backends trycker du på *F5* i Visual Studio. Det finns dock några ytterligare överväganden när du använder autentisering.

Du måste ha en molnbaserad mobilapp med App Service Authentication/Auktorisering konfigurerad och din klient måste ha molnslutpunkten angiven som alternativ inloggningsvärd. Mer information finns i dokumentationen för klientplattformen för de specifika steg som krävs.

Kontrollera att din mobila servergrupp har [Microsoft.Azure.Mobile.Server.Authentication] installerat. Lägg sedan till följande i programmets OWIN-startklass när `MobileAppConfiguration` du `HttpConfiguration`har tillämpats på din:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

I föregående exempel bör du konfigurera *programinställningarna authAudience* och *authIssuer* i filen Web.config till var och en vara URL för programroten med hjälp av HTTPS-schemat. På samma sätt bör du ange *authSigningKey* till värdet av programmets signeringsnyckel.
Så här hämtar du signeringsnyckeln:

1. Navigera till din app i [Azure-portalen]
2. Klicka på **Verktyg**, **Kudu**, **Go**.
3. Klicka på **Miljö**på webbplatsen Kudu Management .
4. Hitta värdet för *\_WEBBPLATS\_\_AUTH SIGNERINGSNYCKEL*.

Använd signeringsnyckeln för parametern *authSigningKey* i den lokala programkonfigurationen.  Din mobila serverdel är nu utrustad för att validera token när du kör lokalt, vilket klienten hämtar token från den molnbaserade slutpunkten.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure-portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Snabbstart för Microsoft.Azure.Mobile.Server.]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Autentisering]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Logga in]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Meddelanden om Microsoft.Azure.Mobile.Server.]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
