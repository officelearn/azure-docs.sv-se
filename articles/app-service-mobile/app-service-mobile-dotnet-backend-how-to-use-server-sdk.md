---
title: Hur du arbetar med SDK för .NET-serverdelen för Mobile Apps | Microsoft Docs
description: Lär dig hur du arbetar med SDK för .NET-serverdelen för Azure App Service Mobile Apps.
keywords: App service, azure apptjänst, mobilapp, mobiltjänsten, skala, skalbar distribution, azure app appdistribution
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 33e968ac608c393d65f69bfd6abbc0d205fb9bd9
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718885"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Arbeta med SDK för .NET-serverdelar för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Det här avsnittet visar hur du använder .NET-serverdelen SDK i Azure App Service Mobile Apps nyckelscenarier. SDK för Azure Mobile Apps kan du arbeta med mobila klienter från ASP.NET-programmet.

> [!TIP]
> Den [.NET server SDK för Azure Mobile Apps] [ 2] är öppen källkod på GitHub. Databasen innehåller all källkod inklusive testsvit för hela servern SDK-enhet och vissa exempelprojekt.
>
>

## <a name="reference-documentation"></a>Referensdokumentation
Referensdokumentation för SDK-servern finns här: [.NET-referens för Azure Mobile Apps][1].

## <a name="create-app"></a>Hur: Skapa en Mobilapp för .NET-serverdel
Om du startar ett nytt projekt, kan du skapa en App Service-programmet med hjälp av antingen den [Azure Portal] eller Visual Studio. Du kan köra App Service-programmet lokalt eller publicera projektet på molnbaserad Apptjänst mobilappen.

Om du lägger till mobilfunktioner i ett befintligt projekt kan se den [ladda ned och initiera SDK](#install-sdk) avsnittet.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Skapa en .NET-serverdel i Azure Portal
Så skapar du en App Service-mobilserverdel antingen den [snabbsjälvstudien] [ 3] eller Följ dessa steg:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

I den *Kom igång* bladet under **skapa ett tabell-API**, Välj **C#** som din **serverdelsspråk**. Klicka på **hämta**, extrahera de komprimerade projektfilerna till den lokala datorn och öppnar du lösningen i Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Skapa en .NET-serverdel som använder Visual Studio 2017

Installera Azure-arbetsbelastning via installationsprogrammet för Visual Studio för att publicera till Azure Mobile Apps-projekt från Visual Studio. När du har installerat SDK kan du skapa ett ASP.NET-program med följande steg:

1. Öppna den **nytt projekt** dialogrutan (från **filen** > **New** > **projekt...** ).
2. Expandera **Visual C#** och välj **Web**.
3. Välj **ASP.NET-Webbtillämpningsprogram (.NET Framework)**.
4. Fyll i projektets namn. Klicka sedan på **OK**.
5. Välj **Azure-Mobilapp** i listan med mallar.
6. Klicka på **OK** att skapa lösningen.
7. Högerklicka på projektet i den **Solution Explorer** och välj **publicera...** , välj sedan **Apptjänst** som publiceringsmålet.
8. Följ anvisningarna för att autentisera och välj en ny eller befintlig Azure App Service för att publicera.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Skapa en .NET-serverdel som använder Visual Studio 2015

Installera den [Azure SDK för .NET] [ 4] (version 2.9.0 eller senare) att skapa en Azure Mobile Apps-projekt i Visual Studio. När du har installerat SDK kan du skapa ett ASP.NET-program med följande steg:

1. Öppna den **nytt projekt** dialogrutan (från **filen** > **New** > **projekt...** ).
2. Expandera **mallar** > **Visual C#**, och välj **Web**.
3. Välj **ASP.NET-webbapp**.
4. Fyll i projektets namn. Klicka sedan på **OK**.
5. Under *ASP.NET 4.5.2-mallar mallar*väljer **Azure-Mobilapp**. Kontrollera **värd i molnet** att skapa en mobilserverdel i molnet som du kan publicera det här projektet.
6. Klicka på **OK**.

## <a name="install-sdk"></a>Hur: Ladda ned och initiera SDK
SDK är tillgänglig på [NuGet.org]. Det här paketet innehåller den grundläggande funktion som krävs för att komma igång med SDK: N. Om du vill initiera SDK som du behöver utföra åtgärder på den **HttpConfiguration** objekt.

### <a name="install-the-sdk"></a>Installera SDK:n
Om du vill installera SDK: N, högerklickar du på serverprojekt i Visual Studio väljer **hantera NuGet-paket**, Sök efter den [Microsoft.Azure.Mobile.Server] paketet och klicka sedan på **installera** .

### <a name="server-project-setup"></a> Initiera serverprojektet
En server för .NET serverdelsprojekt initieras liknar andra ASP.NET-projekt genom att inkludera en OWIN-startklass. Se till att du har refererar till NuGet-paketet `Microsoft.Owin.Host.SystemWeb`. Högerklicka på serverprojektet för att lägga till den här klassen i Visual Studio och välj **Lägg till** >
**nytt objekt**, sedan **Web**  >  ** Allmän** > **OWIN-startklass**.  En klass skapas med följande attribut:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

I den `Configuration()` -metoden för din OWIN-startklass, Använd en **HttpConfiguration** objekt för att konfigurera Azure Mobile Apps-miljö.
I följande exempel initieras serverprojekt med inga nya funktioner:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Om du vill aktivera enskilda funktioner måste du anropa tilläggsmetoder på den **MobileAppConfiguration** objektet innan du anropar **gällafoderråvaran**. Till exempel följande kod lägger till standardvägar till alla API-kontrollenheter som har attributet `[MobileAppController]` under initieringen:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Server-Snabbstart från Azure-portalen anrop **UseDefaultConfiguration()**. Det här motsvarar följande inställningar:

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

* `AddMobileAppHomeController()` innehåller standardstartsidan för Azure Mobile Apps.
* `MapApiControllers()` tillhandahåller anpassade API-funktioner för WebAPI-styrenheter dekorerad med den `[MobileAppController]` attribut.
* `AddTables()` innehåller en mappning av den `/tables` slutpunkter till tabellen domänkontrollanter.
* `AddTablesWithEntityFramework()` är en kort hand för mappning av den `/tables` slutpunkter med Entity Framework-baserade domänkontrollanter.
* `AddPushNotifications()` ger en enkel metod för att registrera enheter för Notification hub.
* `MapLegacyCrossDomainController()` innehåller standard CORS-huvuden för lokal utveckling.

### <a name="sdk-extensions"></a>SDK-tillägg
Följande NuGet-baserade tilläggspaket har olika mobila funktioner som kan användas av ditt program. Du aktiverar tillägg under initieringen med hjälp av den **MobileAppConfiguration** objekt.

* [Microsoft.Azure.Mobile.Server.Quickstart] har stöd för den grundläggande Mobile Apps-installationen. Lagts till i konfigurationen genom att anropa den **UseDefaultConfiguration** tilläggsmetod under initieringen. Det här tillägget innehåller följande tillägg: Meddelanden, autentisering, entiteten, tabeller, mellan domäner och Home-paket. Det här paketet används av den Snabbstart för mobila appar på Azure-portalen.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementerar standard *den här mobilappen är igång sidan* för rot-webbplats. Lägg till i konfigurationen genom att anropa den **AddMobileAppHomeController** tilläggsmetod.
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) innehåller klasser för att arbeta med data och uppsättningar av datapipelinen. Lägg till i konfigurationen genom att anropa den **AddTables** tilläggsmetod.
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) gör det möjligt att komma åt data i SQL-databasen Entity Framework. Lägg till i konfigurationen genom att anropa den **AddTablesWithEntityFramework** tilläggsmetod.
* [Microsoft.Azure.Mobile.Server.Authentication] aktiverar autentisering och uppsättningar av OWIN-mellanprogrammet som används för att validera token. Lägg till i konfigurationen genom att anropa den **AddAppServiceAuthentication** och **IAppBuilder**. **UseAppServiceAuthentication** tilläggsmetoder.
* [Microsoft.Azure.Mobile.Server.Notifications] aktiverar push-meddelanden och definierar en slutpunkt för registrering av push. Lägg till i konfigurationen genom att anropa den **AddPushNotifications** tilläggsmetod.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) skapar en kontrollant som skickar data till äldre webbläsare från din Mobilapp. Lägg till i konfigurationen genom att anropa den **MapLegacyCrossDomainController** tilläggsmetod.
* [Microsoft.Azure.Mobile.Server.Login] AppServiceLoginHandler.CreateToken() metoden, vilket är en statisk metod som används under Anpassad autentiseringsscenarier.

## <a name="publish-server-project"></a>Hur: Publicera serverprojektet
Det här avsnittet visar hur du publicerar din .NET serverdelsprojekt från Visual Studio. Du kan också distribuera projektet serverdelen med [Git](../app-service/deploy-local-git.md) eller någon av de andra metoder tillgängliga där.

1. Återskapa projektet om du vill återställa NuGet-paket i Visual Studio.
2. Högerklicka på projektet i Solution Explorer, klicka på **publicera**. Första gången du publicerar, som du behöver definiera en publiceringsprofil. När du redan har en profil som har definierats kan du markera den och klicka på **publicera**.
3. Om du uppmanas att välja en publiceringsmål, klickar du på **Microsoft Azure App Service** > **nästa**, och sedan (vid behov) logga in med dina autentiseringsuppgifter för Azure.
   Visual Studio laddar ned och lagrar på ett säkert sätt dina publiceringsinställningar direkt från Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Välj din **prenumeration**väljer **resurstyp** från **visa**, expandera **Mobilapp**, och välj din mobilappsserverdel och klicka på **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Kontrollera publicera profilinformation och klicka på **publicera**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    När din mobilappsserverdel har publicerats visas en landningssida som visar att det lyckades.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> Hur: Definiera en
Definiera en Tabellkontrollanten om du vill exponera en SQL-tabell för mobila klienter.  Konfigurera en kräver tre steg:

1. Skapa en klass för Data Transfer objekt (DTO).
2. Konfigurera en tabellreferens i Mobile DbContext-klassen.
3. Skapa en.

Data Transfer objekt (DTO) är en vanlig C#-objekt som ärver från `EntityData`.  Exempel:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO används för att definiera tabell i SQL-databasen.  Om du vill skapa databasposten, lägger du till en `DbSet<>` egenskap DbContext som du använder.  I projektet standardmallen för Azure Mobile Apps i DbContext kallas `Models\MobileServiceContext.cs`:

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

Om du har installerat SDK för Azure kan skapa du nu en mall tabellkontrollanten på följande sätt:

1. Högerklicka på mappen domänkontrollanter och välj **Lägg till** > **Controller...** .
2. Välj den **Azure Mobile Apps Table Controller** och sedan klicka på **Lägg till**.
3. I den **Lägg till styrenhet** dialogrutan:
   * I den **Modellklass** listrutan, Välj ditt nya DTO.
   * I den **DbContext** listrutan Välj Mobile Service DbContext-klassen.
   * Domänkontrollantens namn skapas åt dig.
4. Klicka på **Lägg till**.

Server snabbstartsprojektet innehåller ett exempel på en enkel **TodoItemController**.

### <a name="adjust-pagesize"></a>Hur: Justera sidindelning tabellstorleken
Som standard returnerar Azure Mobile Apps 50 poster per begäran.  Växling säkerställer att klienten inte binder upp sina UI-tråden eller servern för länge, säkerställer en bra användarupplevelse. För att ändra tabellen växlingsfilens storlek, öka ”tillåten frågestorlek” serversidan och klientsidan sidan Ändra storlek på serversidan ”tillåten frågestorlek” justeras med hjälp av den `EnableQuery` attribut:

    [EnableQuery(PageSize = 500)]

Se till att PageSize är samma eller större än den storlek som begärs av klienten.  Referera till specifika klient HOWTO-dokumentationen för mer information om hur du ändrar storleken på klienten.

## <a name="how-to-define-a-custom-api-controller"></a>Anvisningar: Definiera en anpassad API-kontroll
Den anpassade API-kontrollen ger den mest grundläggande funktionen till din mobilappsserverdel genom att exponera en slutpunkt. Du kan registrera en mobil-specifika API-kontroll med attributet [MobileAppController]. Den `MobileAppController` attributet registrerar vägen, ställer in Mobile Apps JSON-serialisering och aktiverar [klientens kontroll av version](app-service-mobile-client-and-server-versioning.md).

1. Högerklicka på mappen domänkontrollanter i Visual Studio och klicka sedan på **Lägg till** > **Controller**väljer **Web API 2 Controller&mdash;tom** och Klicka på **Lägg till**.
2. Ange en **Kontrollnamn**, till exempel `CustomController`, och klicka på **Lägg till**.
3. I den nya domänkontrollant klassfilen lägger du till följande med instruktionen:

        using Microsoft.Azure.Mobile.Server.Config;
4. Tillämpa den **[MobileAppController]** attributet API controller klassdefinitionen, som i följande exempel:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. I App_Start/Startup.MobileApp.cs-filen lägger du till ett anrop till den **MapApiControllers** tilläggsmetod, som i följande exempel:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Du kan också använda den `UseDefaultConfiguration()` tilläggsmetod i stället för `MapApiControllers()`. Alla domänkontrollanter som inte har **MobileAppControllerAttribute** tillämpas kan fortfarande användas av klienter, men det kanske inte korrekt användas av klienter med valfri Mobilapp-klient SDK.

## <a name="how-to-work-with-authentication"></a>Anvisningar: Arbeta med autentisering
Azure Mobile Apps använder App Service-autentisering / auktorisering för att skydda din mobila serverdelstjänst.  Det här avsnittet visar hur du utför följande uppgifter för autentiseringsrelaterade i serverprojektet .NET-serverdelen:

* [Hur: Lägg till autentisering i ett serverprojekt](#add-auth)
* [Hur: Använd anpassad autentisering för ditt program](#custom-auth)
* [Hur: Hämta information om autentiserade användare](#user-info)
* [Hur: Begränsa åtkomst till data för behöriga användare](#authorize)

### <a name="add-auth"></a>Hur: Lägg till autentisering i ett serverprojekt
Du kan lägga till autentisering i serverprojektet genom att utöka den **MobileAppConfiguration** objekt och konfigurera OWIN-mellanprogrammet. När du installerar den [Microsoft.Azure.Mobile.Server.Quickstart] paketet och anropa den **UseDefaultConfiguration** tilläggsmetod, du kan gå vidare till steg 3.

1. I Visual Studio, installerar den [Microsoft.Azure.Mobile.Server.Authentication] paketet.
2. Lägg till följande kodrad i projektfilen Startup.cs i början av den **Configuration** metoden:

        app.UseAppServiceAuthentication(config);

    Den här komponenten för OWIN-mellanprogrammet verifierar token som utfärdas av tillhörande App Service-gateway.
3. Lägg till den `[Authorize]` attributet till valfri domänkontrollant eller en metod som kräver autentisering.

Läs mer om hur du autentiserar klienter till din Mobile Apps-serverdel, i [Lägg till autentisering i din app](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Hur: Använd anpassad autentisering för ditt program
> [!IMPORTANT]
> För att aktivera anpassad autentisering, måste du först aktivera App Service-autentisering utan att välja en provider för App Service i Azure-portalen. Detta aktiverar miljövariabeln WEBSITE_AUTH_SIGNING_KEY när de ligger.
> 
> 
Om du inte vill använda en App Service autentisering/auktorisering provider kan du implementera inloggningen systemet. Installera den [Microsoft.Azure.Mobile.Server.Login] paket som hjälper till med generering av autentisering.  Ange din egen kod för att verifiera användarens autentiseringsuppgifter. Du kan exempelvis söka mot saltat och hashade lösenord i en databas. I exemplet nedan, den `isValidAssertion()` metod (definieras någon annanstans) ansvarar för dessa kontroller.

Anpassad autentisering exponeras genom att skapa en ApiController och exponera `register` och `login` åtgärder. Klienten bör använda ett anpassat gränssnitt för att samla in information från användaren.  Informationen skickas sedan till API med en HTTP POST-standardanrop. När servern validerar kontrollen, ett token utfärdas med hjälp av den `AppServiceLoginHandler.CreateToken()` metoden.  ApiController **bör inte** använder den `[MobileAppController]` attribut.

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

I föregående exempel är LoginResult och LoginResultUser serialiserbara objekt med synliga nödvändiga egenskaper. Klienten förväntar sig svar för denna inloggning som ska returneras som JSON-objekt i formatet:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Den `AppServiceLoginHandler.CreateToken()` metoden innehåller en *målgrupp* och en *utfärdare* parametern. Båda parametrarna är inställda på Webbadressen till din programrot med hjälp av HTTPS-schema. På samma sätt bör du ange *secretKey* vara signeringsnyckeln för värdet för ditt program. Distribuera inte signeringsnyckeln i en klient som den kan användas för att myntverket nycklar och Personifiera användare. Du kan hämta signeringsnyckeln medan finns i App Service genom att referera till den *webbplats\_AUTH\_signering\_nyckel* miljövariabeln. Om det behövs i en lokal felsökning kontext, följer du anvisningarna i den [lokal felsökning med autentisering](#local-debug) avsnitt för att hämta nyckeln och spara den som en programinställning.

Utfärdad token kan också omfatta andra anspråk och ett förfallodatum.  Minimalt, utfärdad token måste innehålla ett ämne (**sub**) anspråk.

Du kan använda standard klienten `loginAsync()` metoden genom att överbelasta autentisering vägen.  Om klienten anropar `client.loginAsync('custom');` för att logga in färdvägen måste vara `/.auth/login/custom`.  Du kan ange rutten för anpassad autentisering domänkontrollern med `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Med hjälp av den `loginAsync()` sätt säkerställs att autentiseringstoken är kopplad till varje efterföljande anrop till tjänsten.
>
>

### <a name="user-info"></a>Hur: Hämta information om autentiserade användare
När en användare autentiseras av App Service kan du komma åt tilldelade användar-ID och annan information i din kod för .NET-serverdel. Användarinformationen kan användas för att göra auktoriseringsbeslut i serverdelen. Följande kod hämtar användar-ID som är associerade med en begäran:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID härleds från provider-specifik användar-ID och är statisk för en viss användare och inloggningsprovider.  SID är null för ogiltig autentiseringstoken.

App Service kan du be din inloggningsprovider specifika anspråk. Varje identitetsprovidern kan ge mer information med identitetsprovider SDK.  Du kan till exempel använda av Facebook Graph API för vänner information.  Du kan ange anspråk som begärs på provider-bladet i Azure-portalen. Vissa anspråk kräva ytterligare konfiguration med identitetsprovidern.

I följande kod anropar den **GetAppServiceIdentityAsync** tilläggsmetod att hämta de autentiseringsuppgifter för inloggning, bland annat åtkomst-token som behövs för att göra förfrågningar mot ett Facebook Graph API:

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

Lägg till en med hjälp av instruktionen för `System.Security.Principal` att tillhandahålla den **GetAppServiceIdentityAsync** tilläggsmetod.

### <a name="authorize"></a>Hur: Begränsa åtkomst till data för behöriga användare
I det föregående avsnittet visade vi hur du hämtar det användar-ID för en autentiserad användare. Du kan begränsa åtkomsten till data och andra resurser baserat på det här värdet. Till exempel är att lägga till en användar-ID-kolumn i tabeller och filtrera resultatet av frågan genom att det användar-ID ett enkelt sätt att begränsa data som returneras till auktoriserade användare. Följande kod returnerar rader med data endast när SID matchar värdet i kolumnen UserId i TodoItem-tabellen:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Den `Query()` metoden returnerar en `IQueryable` som kan ändras av LINQ att hantera filtrering.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Anvisningar: Lägg till push-meddelanden till en serverprojekt
Lägg till push-meddelanden i serverprojektet genom att utöka den **MobileAppConfiguration** objektet och skapa en Notification Hub-klient.

1. I Visual Studio högerklickar du på serverprojektet och klicka på **hantera NuGet-paket**, Sök efter `Microsoft.Azure.Mobile.Server.Notifications`, klicka sedan på **installera**.
2. Upprepa steget för att installera den `Microsoft.Azure.NotificationHubs` paket som innehåller klientbiblioteket för Meddelandehubbar.
3. I App_Start/Startup.MobileApp.cs, och Lägg till ett anrop till den **AddPushNotifications()** tilläggsmetod under initieringen:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Lägg till följande kod som skapar en Notification Hub-klient:

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

Du kan nu använda Notification Hubs-klienten för att skicka push-meddelanden till registrerade enheter. Mer information finns i [Lägg till push-meddelanden till din app](app-service-mobile-ios-get-started-push.md). Läs mer om Meddelandehubbar i [översikt över Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Hur: Aktivera riktade push-meddelanden med hjälp av taggar
Meddelandehubbar kan du skicka riktade meddelanden till specifika registreringar med hjälp av taggar. Flera taggar skapas automatiskt:

* Installations-ID identifierar en specifik enhet.
* Det användar-Id som baseras på autentiserade SID identifierar en specifik användare.

Installationen ID kan nås från den **installationId** egenskap på den **MobileServiceClient**.  I följande exempel visas hur du använder ett installations-ID för att lägga till en tagg till en specifik installation i Meddelandehubbar:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Taggar som anges av klienten vid push-meddelanderegistreringen ignoreras av serverdelen när du skapar installationen. Om du vill aktivera en klient för att lägga till taggar i installationen, måste du skapa en anpassad API som lägger till taggar med hjälp av föregående mönstret.

Se [klienten lagt till push-meddelande taggar] [ 5] i App Service Mobile Apps slutfört snabbstarten exemplet ett exempel.

## <a name="push-user"></a>Hur: Skicka push-meddelanden till en autentiserad användare
När en autentiserad användare registrerar för push-meddelanden, läggs en användartagg ID automatiskt till registreringen. Du kan skicka push-meddelanden till alla enheter som registrerats av personen med hjälp av den här taggen. Följande kod hämtar SID för användaren som skickar förfrågan och skickar en push-meddelande från en mall till varje enhetsregistrering för den personen:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Vid registrering av push-meddelanden från en autentiserad klient, se till att autentiseringen har slutförts innan du försöker registrera. Mer information finns i [Push till användare] [ 6] i App Service Mobile Apps-exemplet för slutförda Snabbstart för .NET-serverdel.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Anvisningar: Felsök och felsöka .NET Server SDK
Azure App Service tillhandahåller flera felsökning och felsökningstekniker för ASP.NET-program:

* [Övervaka en Azure App Service](../app-service/web-sites-monitor.md)
* [Aktivera Diagnostisk loggning i Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Felsöka en Azure App Service i Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Loggning
Du kan skriva till diagnostikloggar för App Service med hjälp av standard ASP.NET-spårning skrivning. Innan du kan skriva till loggar, måste du aktivera diagnostik i din mobilappsserverdel.

Aktivera diagnostik och skriva till loggar:

1. Följ stegen i [så här aktiverar du diagnostik](../app-service/troubleshoot-diagnostic-logs.md#enablediag).
2. Lägg till följande med instruktionen i kodfilen:

        using System.Web.Http.Tracing;
3. Skapa en spårning skrivare att skriva från .NET-serverdel till diagnostikloggar, enligt följande:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Publicera serverprojektet och komma åt serverdelen för Mobilappen för att köra kodsökvägar med loggning.
5. Ladda ned och utvärdera loggarna, enligt beskrivningen i [så här: Ladda ned loggar](../app-service/troubleshoot-diagnostic-logs.md#download).

### <a name="local-debug"></a>Lokal felsökning med autentisering
Du kan köra programmet lokalt för att testa ändringarna innan du publicerar dem till molnet. För de flesta Azure Mobile Apps-serverdelen, trycker du på *F5* när den är i Visual Studio. Det finns dock några ytterligare överväganden när du använder autentisering.

Du måste ha en molnbaserad mobilappen med App Service autentisering/auktorisering konfigurerad och klienten måste ha molnslutpunkten anges som alternativa inloggnings-värden. Finns i dokumentationen för din klientplattform för de specifika steg som krävs.

Kontrollera att din mobila serverdelstjänst har [Microsoft.Azure.Mobile.Server.Authentication] installerad. Sedan, i ditt programs OWIN-startklass, Lägg till följande, efter `MobileAppConfiguration` har tillämpats på din `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

I föregående exempel, bör du konfigurera den *authAudience* och *authIssuer* programinställningar i din Web.config filen till varje vara URL för din programrot med hjälp av HTTPS-schema. På samma sätt bör du ange *authSigningKey* vara signeringsnyckeln för värdet för ditt program.
Du kan hämta signeringsnyckeln:

1. Gå till din app i den [Azure Portal]
2. Klicka på **verktyg**, **Kudu**, **Gå**.
3. I hantering av Kudu-webbplatsen klickar du på **miljö**.
4. Hitta värdet för *webbplats\_AUTH\_signering\_NYCKELN*.

Använda signeringsnyckeln för den *authSigningKey* parameter i config lokalt program.  Din mobila serverdelstjänst är nu utrustad att validera token vid lokal körning som klienten hämtar token från den molnbaserade slutpunkten.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure Portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
