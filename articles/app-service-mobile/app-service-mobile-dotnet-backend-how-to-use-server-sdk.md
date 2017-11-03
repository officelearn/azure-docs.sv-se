---
title: "Hur du arbetar med serverdelen .NET SDK för Mobile Apps | Microsoft Docs"
description: "Lär dig hur du arbetar med serverdelen .NET SDK för Azure Apptjänst Mobilappar."
keywords: "App service, azure app service, mobilapp, mobiltjänsten, skala, skalbara och app-distribution, azure app-distribution"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 1728e1d76f075eae8f5500afa34674785f8e3848
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Arbeta med SDK för .NET-serverdelar för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Det här avsnittet visar hur du använder .NET-serverdelen SDK i Azure Apptjänst Mobilappar scenarier. Azure Mobile Apps-SDK hjälper dig att arbeta med mobila klienter från ASP.NET-programmet.

> [!TIP]
> Den [server .NET SDK för Azure Mobile Apps] [ 2] är öppen källkod på GitHub. Databasen innehåller all källkod inklusive hela servern SDK enhet test suite och vissa exempelprojekt.
>
>

## <a name="reference-documentation"></a>Referensdokumentation
Referensdokumentationen för SDK-servern finns här: [.NET-referens för Azure Mobile Apps][1].

## <a name="create-app"></a>Så här: skapa en .NET-mobilappsserverdel
Om du startar ett nytt projekt, kan du skapa ett program för användning med antingen den [Azure-portalen] eller Visual Studio. Du kan köra programmet Apptjänst lokalt eller publicera projektet till molnbaserade Apptjänst mobilappen.

Om du lägger till mobila funktioner till ett befintligt projekt, finns det [ladda ned och initiera SDK](#install-sdk) avsnitt.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Skapa en .NET-serverdel i Azure Portal
Så skapar du en Apptjänst mobilserverdel antingen den [Snabbstartsguide] [ 3] eller följa dessa steg:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

I den *Kom igång* bladet under **skapa en tabell API**, Välj **C#** som din **språk för serverdel**. Klicka på **hämta**extrahera de komprimerade projektfilerna till den lokala datorn och öppna lösningen i Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Skapa en .NET-serverdel med hjälp av Visual Studio 2013 och Visual Studio 2015
Installera den [Azure SDK för .NET] [ 4] (version 2.9.0 eller senare) att skapa ett Azure Mobile Apps-projekt i Visual Studio. När du har installerat SDK kan du skapa ett ASP.NET-program med följande steg:

1. Öppna den **nytt projekt** dialogrutan (från *filen* > **ny** > **projekt...** ).
2. Expandera **mallar** > **Visual C#**, och välj **Web**.
3. Välj **ASP.NET-webbprogram**.
4. Fyll i projektets namn. Klicka sedan på **OK**.
5. Under *ASP.NET 4.5.2-mallen mallar*väljer **Azure Mobile App**. Kontrollera **värd i molnet** du skapar en mobilserverdel i molnet som du kan publicera det här projektet.
6. Klicka på **OK**.

## <a name="install-sdk"></a>Så här: hämta och initiera SDK
SDK är tillgänglig på [NuGet.org]. Det här paketet innehåller grundläggande funktioner som krävs för att komma igång med SDK. Om du vill initiera SDK, som du behöver utföra åtgärder på den **HttpConfiguration** objekt.

### <a name="install-the-sdk"></a>Installera SDK:n
För att installera SDK: N, högerklicka på serverprojekt i Visual Studio, markera **hantera NuGet-paket**, söka efter den [Microsoft.Azure.Mobile.Server] paketet och klicka sedan på **installera**.

### <a name="server-project-setup"></a>Initiera serverprojektet
Ett .NET-serverdel serverprojekt initieras liknar andra ASP.NET-projekt genom att lägga till en OWIN-startklass. Se till att du har refererade NuGet-paketet `Microsoft.Owin.Host.SystemWeb`. Högerklicka på serverprojektet för att lägga till den här klassen i Visual Studio och välj **Lägg till** >
**nytt objekt**, sedan **Web** > **allmänna** > **OWIN-startklass**.  En klass skapas med följande attribut:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

I den `Configuration()` metod för din OWIN-startklass, Använd en **HttpConfiguration** objekt för att konfigurera Azure Mobile Apps.
I följande exempel initierar serverprojekt med inga extra funktioner:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Om du vill aktivera enskilda funktioner, måste du anropa tilläggsmetoder på den **MobileAppConfiguration** objektet innan du anropar **gällafoderråvaran**. Till exempel följande kod lägger till standardvägar till alla API-domänkontrollanter som har attributet `[MobileAppController]` under initieringen:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Server-Snabbstart från Azure portal anrop **UseDefaultConfiguration()**. Det här motsvarar följande inställningar:

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

Tillägget metoderna är:

* `AddMobileAppHomeController()`ger standardstartsida för Azure Mobile Apps.
* `MapApiControllers()`innehåller anpassade API-funktioner för WebAPI domänkontrollanter dekorerad med den `[MobileAppController]` attribut.
* `AddTables()`innehåller en mappning av den `/tables` slutpunkter till domänkontrollanter för tabellen.
* `AddTablesWithEntityFramework()`är en kort hand för mappning av `/tables` slutpunkter som använder Entity Framework-baserade domänkontrollanter.
* `AddPushNotifications()`ger en enkel metod för att registrera enheter för Notification Hubs.
* `MapLegacyCrossDomainController()`innehåller standard CORS-huvuden för lokal utveckling.

### <a name="sdk-extensions"></a>SDK-tillägg
Följande NuGet-baserade tilläggspaket har olika mobila funktioner som kan användas av ditt program. Du aktiverar tillägg under initiering med hjälp av den **MobileAppConfiguration** objekt.

* [Microsoft.Azure.Mobile.Server.Quickstart] stöder grundläggande Mobile Apps-inställningar. Lagts till i konfigurationen genom att anropa den **UseDefaultConfiguration** tilläggsmetoden under initieringen. Det här tillägget ingår följande tillägg: meddelanden, autentisering, enhet, tabeller, domäner och Home paket. Det här paketet används av Mobile Apps Quickstart tillgängliga på Azure-portalen.
* [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementerar standard *mobila appen är igång sidan* för roten för webbplatsen. Lägg till i konfigurationen genom att anropa den **AddMobileAppHomeController** metod.
* [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) innehåller klasser för att arbeta med data och anger upp data pipeline. Lägg till i konfigurationen genom att anropa den **AddTables** metod.
* [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) aktiverar av Entity Framework att komma åt data i SQL-databasen. Lägg till i konfigurationen genom att anropa den **AddTablesWithEntityFramework** metod.
* [Microsoft.Azure.Mobile.Server.Authentication] aktiverar autentisering och anger upp OWIN-mellanprogram som används för att validera token. Lägg till i konfigurationen genom att anropa den **AddAppServiceAuthentication** och **IAppBuilder**. **UseAppServiceAuthentication** tilläggsmetoder.
* [Microsoft.Azure.Mobile.Server.Notifications] aktiverar push-meddelanden och definierar en slutpunkt för push-registrering. Lägg till i konfigurationen genom att anropa den **AddPushNotifications** metod.
* [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) skapar en domänkontrollant som levererar data till äldre webbläsare från din Mobilapp. Lägg till i konfigurationen genom att anropa den **MapLegacyCrossDomainController** metod.
* [Microsoft.Azure.Mobile.Server.Login] ger metoden AppServiceLoginHandler.CreateToken(), som är en statisk metod som används under scenarier för anpassad autentisering.

## <a name="publish-server-project"></a>Så här: publicera serverprojektet
Det här avsnittet visar hur du publicerar serverdelsprojektet .NET från Visual Studio. Du kan också distribuera projektet backend med [Git](../app-service/app-service-deploy-local-git.md) eller någon av de andra metoder tillgängliga där.

1. Bygg projektet om du vill återställa NuGet-paket i Visual Studio.
2. Högerklicka på projektet i Solution Explorer, klicka på **publicera**. Första gången du publicerar måste du definiera en publiceringsprofilen. När du redan har en profil som har definierats, markerar du den och klicka på **publicera**.
3. Om du uppmanas att välja ett publiceringsmål, klickar du på **Microsoft Azure App Service** > **nästa**, och sedan (vid behov) logga in med dina autentiseringsuppgifter för Azure.
   Visual Studio hämtar och lagrar på ett säkert sätt dina publiceringsinställningar direkt från Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Välj din **prenumeration**väljer **resurstypen** från **visa**, expandera **Mobilapp**, och serverdelen för Mobilappen klickar **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Kontrollerar profilinformation publicera och klickar på **publicera**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    När din mobilappsserverdel har publicerats visas en landningssida som anger lyckades.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Så här: definiera en tabell-styrenhet
Definiera en tabell domänkontrollant om du vill exponera en SQLtabell för mobila klienter.  Konfigurera en tabell Controller kräver tre steg:

1. Skapa en klass för Data Transfer objekt (DTO).
2. Konfigurera en tabellreferens i Mobile DbContext-klassen.
3. Skapa en tabell-styrenhet.

En Data Transfer objekt (DTO) är ett vanligt C#-objekt som ärver från `EntityData`.  Exempel:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO används för att definiera tabell i SQL-databasen.  Om du vill skapa en post i databasen, lägger du till en `DbSet<>` egenskapen DbContext som du använder.  I projektet standardmallen för Azure Mobile Apps i DbContext kallas `Models\MobileServiceContext.cs`:

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

Om du har installerat Azure SDK kan skapa du nu en mall för tabellen domänkontrollant på följande sätt:

1. Högerklicka på mappen domänkontrollanter och välj **Lägg till** > **domänkontrollant...** .
2. Välj den **Azure Mobile Apps tabell Controller** alternativ och klicka sedan på **Lägg till**.
3. I den **Lägg till styrenhet** dialogrutan:
   * I den **Modellklass** listrutan, Välj din nya DTO.
   * I den **DbContext** listrutan, Välj mobila tjänsten DbContext-klassen.
   * Domänkontrollantens namn skapas åt dig.
4. Klicka på **Lägg till**.

Server snabbstartsprojektet innehåller ett exempel på en enkel **TodoItemController**.

### <a name="adjust-pagesize"></a>Så här: justera tabellen växlingsfilens storlek
Som standard returnerar Azure Mobile Apps 50 poster per begäran.  Sidindelning garanterar att klienten inte binder upp sina UI-tråden eller servern under för lång tid att säkerställa en bra användarupplevelse. Om du vill ändra tabellens växlingsfilens storlek ökar serversidan ”tillåtna storleken för frågan” och klientsidan sidstorlek serversidan ”tillåtna storleken för frågan” justeras med hjälp av den `EnableQuery` attribut:

    [EnableQuery(PageSize = 500)]

Kontrollera PageSize samma eller större än den storlek som begärs av klienten.  Referera till specifika klienten ta dokumentationen för mer information om hur du ändrar storleken på klienten.

## <a name="how-to-define-a-custom-api-controller"></a>Så här: definiera en anpassad API-styrenhet
Anpassade API-kontrollanten ger den mest grundläggande funktionen till din mobilappsserverdel genom att exponera en slutpunkt. Du kan registrera en mobil-specifika API-kontrollanten med attributet [MobileAppController]. Den `MobileAppController` attributet registrerar flödet, ställer in Mobile Apps JSON-serialisering och aktiverar [klienten versionskontroll](app-service-mobile-client-and-server-versioning.md).

1. Högerklicka på mappen domänkontrollanter i Visual Studio och klicka sedan på **Lägg till** > **domänkontrollant**väljer **Web API 2-styrenhet&mdash;tom** och på **Lägg till**.
2. Ange en **Kontrollnamn**, som `CustomController`, och klicka på **Lägg till**.
3. I den nya domänkontrollant klassfilen lägger du till följande med instruktionen:

        using Microsoft.Azure.Mobile.Server.Config;
4. Tillämpa den **[MobileAppController]** attribut till API-styrenhet klassdefinitionen, som i följande exempel:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. App_Start/Startup.MobileApp.cs filen, lägga till ett anrop till den **MapApiControllers** metod, som i följande exempel:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Du kan också använda den `UseDefaultConfiguration()` metod i stället för `MapApiControllers()`. Alla domänkontrollanter som inte har **MobileAppControllerAttribute** tillämpas kan fortfarande användas av klienter, men det kanske inte korrekt förbrukas av klienter som använder en Mobilapp klient-SDK.

## <a name="how-to-work-with-authentication"></a>Så här: arbeta med autentisering
Azure Mobile Apps använder App Service-autentisering / auktorisering för att skydda din mobila serverdel.  Det här avsnittet visar hur du utför följande autentisering-relaterade uppgifter i projektet .NET backend-servern:

* [Så här: Lägg till autentisering i ett serverprojekt](#add-auth)
* [Så här: Använd anpassad autentisering för ditt program](#custom-auth)
* [Så här: hämta autentiserad användarinformation](#user-info)
* [Så här: begränsa åtkomst till data för behöriga användare](#authorize)

### <a name="add-auth"></a>Så här: Lägg till autentisering i ett serverprojekt
Du kan lägga till autentisering i serverprojektet genom att utöka den **MobileAppConfiguration** objekt och konfigurera OWIN mellanprogram. När du installerar den [Microsoft.Azure.Mobile.Server.Quickstart] paket- och anrop i **UseDefaultConfiguration** metod, du kan gå vidare till steg 3.

1. I Visual Studio, installera den [Microsoft.Azure.Mobile.Server.Authentication] paketet.
2. Lägg till följande kodrad i projektfilen Startup.cs i början av den **Configuration** metoden:

        app.UseAppServiceAuthentication(config);

    Den här OWIN mellanprogram komponenten verifierar token som utfärdats av en associerad App Service-gateway.
3. Lägg till den `[Authorize]` attributet till domänkontrollanten eller metoden som kräver autentisering.

Läs om hur du autentiserar klienter till din Mobile Apps-serverdel i [Lägg till autentisering i appen](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Så här: Använd anpassad autentisering för ditt program
Du kan implementera inloggningen systemet om du inte vill använda en av leverantörerna för autentisering/auktorisering i Apptjänst. Installera den [Microsoft.Azure.Mobile.Server.Login] paketet för att underlätta autentisering-token generering.  Ange egen kod vid verifiering av autentiseringsuppgifter. Du kan exempelvis kontrollera mot saltat och hashformaterats lösenord i en databas. I exemplet nedan är de `isValidAssertion()` metod (definierat någon annanstans) ansvarar för dessa kontroller.

Anpassad autentisering exponeras genom att skapa en ApiController och exponera `register` och `login` åtgärder. Klienten bör använda ett anpassat gränssnitt för att samla in information från användaren.  Informationen skickas sedan till API: et med ett standard HTTP POST-anrop. När servern verifierar kontrollen, ett token utfärdas med hjälp av den `AppServiceLoginHandler.CreateToken()` metoden.  ApiController **bör inte** använder den `[MobileAppController]` attribut.

Ett exempel `login` åtgärd:

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

I föregående exempel är LoginResult och LoginResultUser serialiserbara objekt med synliga nödvändiga egenskaper. Klienten förväntar sig inloggningen svar returneras som JSON-objekt i formatet:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Den `AppServiceLoginHandler.CreateToken()` metoden innehåller en *målgruppen* och en *utfärdaren* parameter. Båda parametrarna har angetts till URL-Adressen för din programrot med HTTPS-schema. På samma sätt bör du ange *secretKey* ska värdet för ditt program signeringsnyckel. Distribuera inte signeringsnyckeln i en klient som kan användas för att myntverket nycklar och Personifiera användare. Du kan hämta signeringsnyckeln medan finns i App Service genom att referera till den *webbplats\_AUTH\_signering\_NYCKELN* miljövariabeln. Om det behövs i en kontext som lokala felsökning, följ instruktionerna i den [lokala felsökning med autentisering](#local-debug) avsnittet för att hämta nyckeln och spara den som en programinställning.

Utfärdad token kan också omfatta andra anspråk och upphör att gälla.  Utfärdad token måste åtminstone innehålla ett ämne (**sub**) anspråk.

Du kan använda standard klienten `loginAsync()` metoden av överbelastning flöde för autentisering.  Om klienten anropar `client.loginAsync('custom');` för att logga in rutten måste vara `/.auth/login/custom`.  Du kan ange väg för anpassad autentisering domänkontrollanten med `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Med den `loginAsync()` metoden gör att autentiseringstoken är ansluten till varje efterföljande anrop till tjänsten.
>
>

### <a name="user-info"></a>Så här: hämta autentiserad användarinformation
När en användare autentiseras av App Service, kan du komma åt tilldelade användar-ID och annan information i serverdelskoden .NET. Informationen kan användas för att göra auktoriseringsbeslut i serverdelen. Följande kod hämtar användar-ID som är associerade med en begäran:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID är härledd från provider-specifik användar-ID och är statisk för en viss användare och inloggningen provider.  SID är null för ogiltig autentiseringstoken.

Apptjänst kan du begära specifika anspråk från leverantören inloggningen. Varje identitetsleverantör kan ge mer information med hjälp av identitetsleverantör SDK.  Du kan till exempel använda Facebook Graph API vänner information.  Du kan ange anspråk som begärs i bladet provider i Azure-portalen. Vissa anspråk kräva ytterligare konfiguration med identitetsleverantören.

Följande koden anropar den **GetAppServiceIdentityAsync** metod att hämta autentiseringsuppgifter för inloggning, bland annat åtkomst-token som behövs för att göra förfrågningar mot Facebook Graph API:

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

Lägg till en med hjälp av instruktionen för `System.Security.Principal` att tillhandahålla den **GetAppServiceIdentityAsync** metod.

### <a name="authorize"></a>Så här: begränsa åtkomst till data för behöriga användare
I det föregående avsnittet visade vi hur du hämtar användar-ID för en autentiserad användare. Du kan begränsa åtkomsten till data och andra resurser baserat på det här värdet. Till exempel är lägga till en kolumn för användar-ID i tabeller och filtrera frågeresultat av användar-ID ett enkelt sätt att begränsa returnerade data till auktoriserade användare. Följande kod returnerar rader med data endast när SID matchar värdet i kolumnen användar-ID i tabellen TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Den `Query()` metoden returnerar en `IQueryable` som kan hanteras av LINQ to hantera filtrering.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Så här: Lägg till push-meddelanden till ett serverprojekt
Lägg till push-meddelanden i serverprojektet genom att utöka den **MobileAppConfiguration** objektet och skapar en klient med Notification Hubs.

1. Högerklicka på serverprojekt i Visual Studio och klicka på **hantera NuGet-paket**, söka efter `Microsoft.Azure.Mobile.Server.Notifications`, klicka på **installera**.
2. Upprepa det här steget för att installera den `Microsoft.Azure.NotificationHubs` paket som innehåller klientbiblioteket Notification Hubs.
3. I App_Start/Startup.MobileApp.cs, och Lägg till ett anrop till den **AddPushNotifications()** tilläggsmetoden under initieringen:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Lägg till följande kod som skapar en Meddelandehubbar klient:

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

Du kan nu använda Notification Hubs-klienten för att skicka push-meddelanden till registrerade enheter. Mer information finns i [Lägg till push-meddelanden i appen](app-service-mobile-ios-get-started-push.md). Mer information om Notification Hubs finns [översikt över Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Så här: aktivera riktade push med hjälp av taggar
Notification Hubs kan du skicka riktade meddelanden till specifika registreringar med hjälp av taggar. Flera etiketter skapas automatiskt:

* Installations-ID identifierar en specifik enhet.
* Det användar-Id som är baserat på den autentiserade SID identifierar en specifik användare.

Installationen ID kan nås från den **installationId** egenskapen på den **MobileServiceClient**.  I följande exempel visas hur du använder ett installations-ID för att lägga till en tagg i en viss Meddelandehubbar-installation:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Alla taggar som angetts av klienten under registreringen av push-meddelanden ignoreras av serverdelen när du skapar installationen. Om du vill aktivera en klient för att lägga till taggar i installationen måste du skapa en anpassad API som lägger till taggar i föregående mönstret.

Se [klienten tillagda push notification taggar] [ 5] i Apptjänst Mobilappar slutförda quickstart provet ett exempel.

## <a name="push-user"></a>Så här: skicka push-meddelanden till en autentiserad användare
När en autentiserad användare registrerar för push-meddelanden, läggs en tagg för användar-ID automatiskt till registreringen. Du kan skicka push-meddelanden till alla enheter som registrerats som personen med hjälp av den här taggen. Följande kod hämtar SID för användaren som skickar förfrågan och skickar en mall för push-meddelanden till varje registrering av enheten för den personen:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

När du registrerar dig för push-meddelanden från en autentiserad klient, se till att autentiseringen har slutförts innan du försöker utföra registreringen. Mer information finns i [push-installera användarna] [ 6] i Apptjänst Mobilappar slutförda quickstart provet för .NET-serverdel.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Så här: felsöka och felsöka SDK för .NET-Server
Azure App Service tillhandahåller flera felsökning och felsökningsmetoder för ASP.NET-program:

* [Övervaka en Azure Apptjänst](../app-service/web-sites-monitor.md)
* [Aktivera diagnostikloggning i Azure App Service](../app-service/web-sites-enable-diagnostic-log.md)
* [Felsöka en Azure Apptjänst i Visual Studio](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Loggning
Du kan skriva till diagnostikloggarna för Apptjänst med hjälp av standard ASP.NET trace skrivning. Innan du kan skriva till loggar, måste du aktivera diagnostik i din mobilappsserverdel.

Aktivera diagnostik och skriva till loggar:

1. Följ stegen i [hur du aktiverar diagnostik](../app-service/web-sites-enable-diagnostic-log.md#enablediag).
2. Lägg till följande med instruktionen i kodfilen:

        using System.Web.Http.Tracing;
3. Skapa en spårning skrivare att skriva från .NET-serverdel till diagnostikloggar, enligt följande:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Publicera serverprojektet och komma åt serverdelen för Mobilappen för att köra kodsökväg med loggning.
5. Hämta och utvärdera loggarna, enligt beskrivningen i [så här: hämta loggar](../app-service/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Lokala felsökning med autentisering
Du kan köra programmet lokalt för att testa ändringar innan du publicerar dem till molnet. Tryck på för de flesta Azure Mobile Apps serverdelar *F5* medan i Visual Studio. Det finns dock några ytterligare överväganden när du använder autentisering.

Du måste ha en molnbaserad mobilapp med autentisering/auktorisering i Apptjänst konfigurerats och klienten måste ha molnslutpunkt som angetts som alternativt inloggnings-värden. Finns i dokumentationen för din klientplattform för de specifika steg som krävs.

Kontrollera att din mobila serverdel har [Microsoft.Azure.Mobile.Server.Authentication] installerad. Sedan, i ditt program OWIN-startklass Lägg till följande, efter `MobileAppConfiguration` har kopplats till din `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

I föregående exempel, bör du konfigurera den *authAudience* och *authIssuer* programinställningar i din Web.config-filen till varje URL: en för din programrot med hjälp av HTTPS-schema. På samma sätt bör du ange *authSigningKey* ska värdet för ditt program signeringsnyckel.
Du kan hämta signeringsnyckeln:

1. Navigera till din app i den [Azure-portalen]
2. Klicka på **verktyg**, **Kudu**, **Gå**.
3. Klicka på hantering av Kudu-plats **miljö**.
4. Hitta värdet för *webbplats\_AUTH\_signering\_NYCKELN*.

Använd signeringsnyckeln för den *authSigningKey* parameter i din lokala program config.  Din mobila serverdel är nu utrustad att validera token när du kör lokalt, som klienten hämtar token från slutpunkten molnbaserade.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure-portalen]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
