---
title: Så här arbetar du med .NET-Server del Server SDK för Mobile Apps | Microsoft Docs
description: Lär dig hur du arbetar med .NET-Server del Server SDK för Azure App Service Mobile Apps.
keywords: App Service, Azure App Service, mobilapp, mobil tjänst, skala, skalbar, app-distribution, Azure App-distribution
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
ms.openlocfilehash: 6481d95db27df9e0b957d61bef24a1cc40b243e7
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025326"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Arbeta med SDK för .NET-serverdelar för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

Det här avsnittet visar hur du använder Server-SDK för .NET-Server delen i nyckel Azure App Service Mobile Apps scenarier. Azure Mobile Apps SDK hjälper dig att arbeta med mobila klienter från ditt ASP.NET-program.

> [!TIP]
> [.NET Server SDK för Azure Mobile Apps][2] är öppen källkod på GitHub. Lagrings platsen innehåller all källkod, inklusive hela Server SDK-enhetens test Suite och vissa exempel projekt.
>
>

## <a name="reference-documentation"></a>Referensdokumentation
Referens dokumentationen för Server-SDK finns här: [Azure Mobile Apps .net-referens][1].

## <a name="create-app"></a>Hur: Skapa en server del för .NET-mobilapp
Om du startar ett nytt projekt kan du skapa ett App Service program med hjälp av antingen [Azure Portal] eller Visual Studio. Du kan köra App Service programmet lokalt eller publicera projektet till din molnbaserade App Service mobilapp.

Om du lägger till mobila funktioner i ett befintligt projekt kan du läsa avsnittet [Hämta och INITIERA SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Skapa en .NET-Server del med hjälp av Azure Portal
Om du vill skapa en App Service mobil Server del följer du antingen [snabb starts guiden][3] eller följer de här stegen:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Gå tillbaka till bladet *Kom igång* , under **skapa ett tabell**-API **C#** , väljer du som **Server dels språk**. Klicka på **Hämta**, extrahera komprimerade projektfiler till den lokala datorn och öppna lösningen i Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Skapa en .NET-Server del med Visual Studio 2017

Installera Azure-arbetsbelastningen via Visual Studio-installationsprogrammet för att publicera till Azure Mobile Apps-projekt från Visual Studio. När du har installerat SDK skapar du ett ASP.NET-program med hjälp av följande steg:

1. Öppna dialog rutan **nytt projekt** (från **fil** > **nytt** > **projekt...** ).
2. Expandera **visuellt C#**  objekt och välj **webb**.
3. Välj **ASP.NET-webbprogram (.NET Framework)** .
4. Fyll i projekt namnet. Klicka sedan på **OK**.
5. Välj **Azure-mobilapp** i listan med mallar.
6. Klicka på **OK** för att skapa lösningen.
7. Högerklicka på projektet i **Solution Explorer** och välj **publicera...** och välj sedan **App Service** som publicerings mål.
8. Följ anvisningarna för att autentisera och välja en ny eller befintlig Azure App Service att publicera.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Skapa en .NET-Server del med Visual Studio 2015

Installera [Azure SDK för .net][4] (version 2.9.0 eller senare) för att skapa ett Azure Mobile Apps-projekt i Visual Studio. När du har installerat SDK skapar du ett ASP.NET-program med hjälp av följande steg:

1. Öppna dialog rutan **nytt projekt** (från **fil** > **nytt** > **projekt...** ).
2. Expandera **mallar** > **visuellt C#** och välj **webb**.
3. Välj **ASP.NET-webbapp**.
4. Fyll i projekt namnet. Klicka sedan på **OK**.
5. Välj **Azure-mobilapp**under *ASP.NET 4.5.2-mallar*. Kontrol lera **värd i molnet** för att skapa en mobil Server del i molnet som du kan publicera projektet i.
6. Klicka på **OK**.

## <a name="install-sdk"></a>Hur: Hämta och initiera SDK
SDK finns på [NuGet.org]. Det här paketet innehåller de grundläggande funktioner som krävs för att komma igång med SDK. För att initiera SDK måste du utföra åtgärder på **HttpConfiguration** -objektet.

### <a name="install-the-sdk"></a>Installera SDK:n
Om du vill installera SDK högerklickar du på Server projektet i Visual Studio, väljer **Hantera NuGet-paket**, söker efter [Microsoft.Azure.Mobile.Server] -paketet och klickar sedan på **Installera**.

### <a name="server-project-setup"></a>Initiera Server projektet
Ett .NET-Server del Server projekt initieras på liknande sätt som andra ASP.NET-projekt, genom att inkludera en OWIN-startklass. Se till att du har refererat till NuGet-paketet `Microsoft.Owin.Host.SystemWeb`. Om du vill lägga till den här klassen i Visual Studio högerklickar du på ditt Server projekt och väljer **Lägg till** >
**nytt objekt**, sedan **Web** > **General**@no__t-**OWIN start klass**.  En klass genereras med följande attribut:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

I metoden `Configuration()` i OWIN-startklassen använder du ett **HttpConfiguration** -objekt för att konfigurera Azure Mobile Apps-miljön.
I följande exempel initieras Server projektet utan några tillagda funktioner:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Om du vill aktivera enskilda funktioner måste du anropa tilläggs metoder för **MobileAppConfiguration** -objektet innan du anropar **ApplyTo**. Följande kod lägger till exempel till standard vägar till alla API-kontrollanter som har attributet `[MobileAppController]` under initieringen:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Servern snabb start från Azure Portal anropar **UseDefaultConfiguration ()** . Detta motsvarar följande konfiguration:

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

De tilläggs metoder som används är:

* `AddMobileAppHomeController()` tillhandahåller standard start sidan för Azure Mobile Apps.
* `MapApiControllers()` tillhandahåller anpassade API-funktioner för WebAPI-kontrollanter dekorerat med attributet `[MobileAppController]`.
* `AddTables()` ger en mappning av `/tables`-slutpunkter till Table controllers.
* `AddTablesWithEntityFramework()` är en kort hand för att mappa `/tables`-slutpunkter med Entity Framework baserade styrenheter.
* `AddPushNotifications()` är ett enkelt sätt att registrera enheter för Notification Hubs.
* `MapLegacyCrossDomainController()` tillhandahåller standard CORS-rubriker för lokal utveckling.

### <a name="sdk-extensions"></a>SDK-tillägg
Följande NuGet-baserade tilläggs paket innehåller olika mobila funktioner som kan användas av ditt program. Du aktiverar tillägg under initieringen med hjälp av **MobileAppConfiguration** -objektet.

* [Microsoft.Azure.Mobile.Server.Quickstart] stöder grundläggande Mobile Apps-installationen. Tillagt i konfigurationen genom att anropa **UseDefaultConfiguration** -tilläggs metoden under initieringen. Tillägget innehåller följande tillägg: Meddelanden, autentisering, entitet, tabeller, kors domän och hem paket. Det här paketet används av den Mobile Apps snabb starten som finns på Azure Portal.
* [Microsoft. Azure. Mobile. Server. Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementerar standardvärdet för *den här mobilappen är upp-och kör-sidan* för webbplatsens rot. Lägg till i konfigurationen genom att anropa **AddMobileAppHomeController** -tilläggs metoden.
* [Microsoft. Azure. Mobile. Server. tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) innehåller klasser för att arbeta med data och konfigurerar data pipelinen. Lägg till i konfigurationen genom att anropa **AddTables** -tilläggs metoden.
* [Microsoft. Azure. Mobile. Server. Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) gör att Entity Framework kan komma åt data i SQL Database. Lägg till i konfigurationen genom att anropa **AddTablesWithEntityFramework** -tilläggs metoden.
* [Microsoft.Azure.Mobile.Server.Authentication] möjliggör autentisering och ställer in de OWIN mellanprogram som används för att validera tokens. Lägg till i konfigurationen genom att anropa **AddAppServiceAuthentication** och **IAppBuilder**. **UseAppServiceAuthentication** -tilläggs metoder.
* [Microsoft.Azure.Mobile.Server.Notifications] aktiverar push-meddelanden och definierar slut punkten för push-registrering. Lägg till i konfigurationen genom att anropa **AddPushNotifications** -tilläggs metoden.
* [Microsoft. Azure. Mobile. Server. CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) skapar en kontrollant som hanterar data till äldre webbläsare från mobilappen. Lägg till i konfigurationen genom att anropa **MapLegacyCrossDomainController** -tilläggs metoden.
* [Microsoft.Azure.Mobile.Server.Login] tillhandahåller metoden AppServiceLoginHandler. skapatoken (), som är en statisk metod som används under anpassade autentiserings scenarier.

## <a name="publish-server-project"></a>Hur: Publicera Server projektet
Det här avsnittet visar hur du publicerar ditt .NET-Server dels projekt från Visual Studio. Du kan också distribuera Server dels projektet med [git](../app-service/deploy-local-git.md) eller någon av de andra metoderna som är tillgängliga där.

1. Bygg om projektet i Visual Studio för att återställa NuGet-paket.
2. I Solution Explorer högerklickar du på projektet och klickar på **publicera**. Första gången du publicerar måste du definiera en publicerings profil. När du redan har definierat en profil kan du markera den och klicka på **publicera**.
3. Om du uppmanas att välja ett publicerings mål klickar du på **Microsoft Azure App Service** > **härnäst**och sedan (vid behov) loggar in med dina Azure-autentiseringsuppgifter.
   Visual Studio hämtar och lagrar dina publicerings inställningar på ett säkert sätt direkt från Azure.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. Välj din **prenumeration**, Välj **resurs typ** från **Visa**, expandera **mobilapp**och klicka på Server del för mobilapp och klicka sedan på **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. Kontrol lera informationen om publicerings profilen och klicka på **publicera**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    När din server del för mobilappen har publicerats visas en landnings sida som visar att det är klart.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a>Hur: Definiera en tabell kontroll enhet
Definiera en tabell kontroll för att visa en SQL-tabell för mobila klienter.  Att konfigurera en tabell styrenhet kräver tre steg:

1. Skapa en Dataöverföring objekts klass (DTO).
2. Konfigurera en tabell referens i mobil DbContext-klassen.
3. Skapa en tabell kontroll enhet.

Ett Dataöverföring-objekt (DTO) är ett C# oformaterat objekt som ärver från `EntityData`.  Exempel:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO används för att definiera tabellen i SQL-databasen.  Om du vill skapa databas posten lägger du till en `DbSet<>`-egenskap i DbContext som du använder.  I standard projekt mal len för Azure Mobile Apps kallas DbContext `Models\MobileServiceContext.cs`:

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

Om du har installerat Azure SDK kan du nu skapa en mall tabell kontrollant på följande sätt:

1. Högerklicka på mappen kontrollanter och välj **Lägg till** > **kontrollant...** .
2. Välj alternativet **Azure Mobile Apps Table Controller** och klicka sedan på **Lägg till**.
3. I dialog rutan **Lägg till kontrollant** :
   * I list rutan **modell klass** väljer du den nya DTO.
   * I list rutan **DbContext** väljer du DbContext-klassen för mobil tjänst.
   * Namnet på kontrollanten skapas åt dig.
4. Klicka på **Lägg till**.

Snabb starts Server projektet innehåller ett exempel för en enkel **TodoItemController**.

### <a name="adjust-pagesize"></a>Hur: Justera tabellens sid storlek
Som standard returnerar Azure Mobile Apps 50 poster per begäran.  Växling ser till att klienten inte knyter upp sin GRÄNSSNITTs tråd eller servern för länge, vilket ger en bättre användar upplevelse. Om du vill ändra tabellens växlings storlek ökar du Server sidans "tillåten frågans storlek" och sidans sid storlek på klient sidan justeras med hjälp av attributet `EnableQuery`:

    [EnableQuery(PageSize = 500)]

Se till att PageSize är samma eller större än den storlek som klienten har begärt.  Mer information om hur du ändrar klient sidans storlek finns i dokumentationen för den aktuella klienten HOWTO.

## <a name="how-to-define-a-custom-api-controller"></a>Anvisningar: Definiera en anpassad API-styrenhet
Den anpassade API-styrenheten innehåller de mest grundläggande funktionerna i Server delen för mobilappen genom att exponera en slut punkt. Du kan registrera en Mobile-Specific API-styrenhet med attributet [MobileAppController]. Attributet `MobileAppController` registrerar vägen, konfigurerar Mobile Apps JSON-serialiseraren och aktiverar [klient versions kontroll](app-service-mobile-client-and-server-versioning.md).

1. I Visual Studio högerklickar du på mappen kontrollanter och klickar sedan på **Lägg till** > **kontrollant**, väljer **webb-API 2 Controller @ no__t-4Empty** och klickar på **Lägg till**.
2. Ange ett **namn på kontrollanten**, till exempel `CustomController`, och klicka på **Lägg till**.
3. I den nya kontroll enhets klass filen lägger du till följande using-instruktion:

        using Microsoft.Azure.Mobile.Server.Config;
4. Använd attributet **[MobileAppController]** för klass definitionen API Controller, som i följande exempel:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. I filen App_Start/startup. MobileApp. cs lägger du till ett anrop till **MapApiControllers** -tilläggs metoden, som i följande exempel:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Du kan också använda tilläggs metoden `UseDefaultConfiguration()` i stället för `MapApiControllers()`. Kontrollanter som inte har **MobileAppControllerAttribute** kan fortfarande användas av klienter, men den kan inte användas på ett korrekt sätt av klienter som använder sig av klient-SDK: n för mobilapp.

## <a name="how-to-work-with-authentication"></a>Anvisningar: Arbeta med autentisering
Azure Mobile Apps använder App Service autentisering/auktorisering för att skydda din mobila Server del.  Det här avsnittet visar hur du utför följande uppgifter som rör autentisering i .NET-Server delens Server projekt:

* [Anvisningar: Lägg till autentisering till ett Server projekt @ no__t-0
* [Anvisningar: Använd anpassad autentisering för ditt program @ no__t-0
* [Anvisningar: Hämta autentiserad användar information @ no__t-0
* [Anvisningar: Begränsa data åtkomst för behöriga användare @ no__t-0

### <a name="add-auth"></a>Hur: Lägg till autentisering till ett Server projekt
Du kan lägga till autentisering till ditt Server projekt genom att utöka **MobileAppConfiguration** -objektet och konfigurera OWIN mellanprogram. När du installerar [Microsoft.Azure.Mobile.Server.Quickstart] paketet och anropar **UseDefaultConfiguration** -tilläggs metoden kan du gå vidare till steg 3.

1. I Visual Studio installerar du paketet [Microsoft.Azure.Mobile.Server.Authentication] .
2. I projekt filen Startup.cs lägger du till följande kodrad i början av **konfigurations** metoden:

        app.UseAppServiceAuthentication(config);

    Den här OWIN mellan-komponenten verifierar token som utfärdats av den associerade App Service gatewayen.
3. Lägg till attributet `[Authorize]` till alla kontrollanter eller metoder som kräver autentisering.

Information om hur du autentiserar klienter till din Mobile Apps Server del finns i [lägga till autentisering i din app](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Hur: Använd anpassad autentisering för ditt program
> [!IMPORTANT]
> Om du vill aktivera anpassad autentisering måste du först aktivera App Service autentisering utan att välja en provider för App Service i Azure Portal. Detta aktiverar WEBSITE_AUTH_SIGNING_KEY-miljövariabeln när den är värd.
> 
> 
> Om du inte vill använda en av App Service-providern för autentisering/auktorisering kan du implementera ditt eget inloggnings system. Installera paketet [Microsoft.Azure.Mobile.Server.Login] för att hjälpa med genereringen av autentiseringstoken.  Ange din egen kod för att verifiera användarautentiseringsuppgifter. Du kan till exempel kontrol lera mot saltade och hashade lösen ord i en databas. I exemplet nedan ansvarar metoden `isValidAssertion()` (definieras någon annan stans) för dessa kontroller.

Den anpassade autentiseringen exponeras genom att skapa en ApiController och exponera `register`-och `login`-åtgärder. Klienten bör använda ett anpassat användar gränssnitt för att samla in informationen från användaren.  Informationen skickas sedan till API: et med ett standard-HTTP POST-anrop. När servern validerar en token utfärdas en token med metoden `AppServiceLoginHandler.CreateToken()`.  ApiController **ska inte** använda attributet `[MobileAppController]`.

Ett exempel på en åtgärd `login`:

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

I föregående exempel är LoginResult och LoginResultUser serialiserbara objekt som visar nödvändiga egenskaper. Klienten förväntar sig att inloggnings svar ska returneras som JSON-objekt i formatet:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Metoden `AppServiceLoginHandler.CreateToken()` innehåller en *mål grupp* och en *Issuer* -parameter. Båda parametrarna anges till URL: en för din program rot med hjälp av HTTPS-schemat. På samma sätt bör du ange *secretKey* som värde för programmets signerings nyckel. Distribuera inte signerings nyckeln i en klient eftersom den kan användas för att mintgrön nycklar och Personifiera användare. Du kan hämta signerings nyckeln i App Service genom att referera till *webbplatsen @ no__t-1AUTH @ no__t-2SIGNING @ no__t-3KEY* . Om det behövs i en lokal fel söknings kontext följer du anvisningarna i avsnittet [lokal fel sökning med autentisering](#local-debug) för att hämta nyckeln och spara den som en program inställning.

Utfärdad token kan också innehålla andra anspråk och förfallo datum.  Som lägsta måste Utfärdad token innehålla ett ämne (**Sub**)-anspråk.

Du kan använda standard-metoden för klient `loginAsync()` genom att överföra Autentiseringstjänsten.  Om klienten anropar `client.loginAsync('custom');` för att logga in måste din väg vara `/.auth/login/custom`.  Du kan ange vägen för den anpassade autentiserings styrenheten med `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> Med hjälp av metoden `loginAsync()` ser du till att autentiseringstoken är kopplat till varje efterföljande anrop till tjänsten.
>
>

### <a name="user-info"></a>Hur: Hämta autentiserad användar information
När en användare autentiseras av App Service kan du komma åt det tilldelade användar-ID: t och annan information i din .NET-backend-kod. Användar informationen kan användas för att fatta auktoriseringsbeslut i Server delen. Följande kod hämtar det användar-ID som är associerat med en begäran:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID härleds från det providerspecifika användar-ID: t och är statiskt för en specifik användare och inloggnings leverantör.  SID är null för ogiltiga autentiseringstoken.

App Service också kan du begära vissa anspråk från din inloggnings leverantör. Varje identitets leverantör kan ge mer information med hjälp av Identity Provider SDK.  Du kan till exempel använda Facebook-Graph API för information om vänner.  Du kan ange anspråk som begärs i Provider-bladet i Azure Portal. Vissa anspråk kräver ytterligare konfiguration med identitets leverantören.

Följande kod anropar tilläggs metoden **GetAppServiceIdentityAsync** för att hämta inloggnings uppgifter, som inkluderar den åtkomsttoken som krävs för att göra förfrågningar mot Facebook-Graph API:

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

Lägg till en using-instruktion för `System.Security.Principal` för att tillhandahålla tilläggs metoden **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Hur: Begränsa data åtkomst för behöriga användare
I föregående avsnitt visade vi hur du hämtar användar-ID för en autentiserad användare. Du kan begränsa åtkomsten till data och andra resurser utifrån det här värdet. Att till exempel lägga till en userId-kolumn i tabeller och filtrera frågeresultaten med hjälp av användar-ID är ett enkelt sätt att begränsa returnerade data endast till behöriga användare. Följande kod returnerar endast data rader när SID matchar värdet i kolumnen UserId i tabellen TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Metoden `Query()` returnerar en `IQueryable` som kan manipuleras av LINQ för att hantera filtrering.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Anvisningar: Lägga till push-meddelanden till ett Server projekt
Lägg till push-meddelanden till ditt Server projekt genom att utöka **MobileAppConfiguration** -objektet och skapa en Notification Hubs-klient.

1. Högerklicka på Server projektet i Visual Studio och klicka på **Hantera NuGet-paket**, sök efter `Microsoft.Azure.Mobile.Server.Notifications` och klicka sedan på **Installera**.
2. Upprepa det här steget för att installera `Microsoft.Azure.NotificationHubs`-paketet, som innehåller Notification Hubs klient biblioteket.
3. I App_Start/startup. MobileApp. CS, och Lägg till ett anrop till **AddPushNotifications ()** -tilläggs metoden under initieringen:

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

Du kan nu använda Notification Hubs-klienten för att skicka push-meddelanden till registrerade enheter. Mer information finns i [Lägg till push-meddelanden till din app](app-service-mobile-ios-get-started-push.md). Mer information om Notification Hubs finns i [Notification Hubs översikt](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="tags"></a>Hur: Aktivera riktad push med Taggar
Med Notification Hubs kan du skicka riktade meddelanden till specifika registreringar med hjälp av taggar. Flera taggar skapas automatiskt:

* Installations-ID: t identifierar en speciell enhet.
* Användar-ID: t baserat på det autentiserade SID: t identifierar en speciell användare.

Installations-ID: t kan nås från egenskapen **installationId** på **MobileServiceClient**.  I följande exempel visas hur du använder ett installations-ID för att lägga till en tagg till en speciell installation i Notification Hubs:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Alla Taggar som anges av klienten under registreringen av push-meddelanden ignoreras av Server delen när installationen skapas. Om du vill att en klient ska kunna lägga till taggar i installationen måste du skapa ett anpassat API som lägger till taggar med föregående mönster.

Se [push-meddelanden som lagts till av klienten][5] i det App Service Mobile Apps slutförda snabb starts exemplet för ett exempel.

## <a name="push-user"></a>Hur: Skicka push-meddelanden till en autentiserad användare
När en autentiserad användare registrerar sig för push-meddelanden läggs en tagg för användar-ID automatiskt till i registreringen. Genom att använda den här taggen kan du skicka push-meddelanden till alla enheter som registrerats av den personen. Följande kod hämtar SID för användaren som gör begäran och skickar ett push-meddelande för en mall till varje enhets registrering för den personen:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

När du registrerar dig för push-meddelanden från en autentiserad klient kontrollerar du att autentiseringen är klar innan du försöker registrera. Mer information finns i [push to Users][6] i App Service Mobile Apps slutförda snabb starts exempel för .NET-Server delen.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Anvisningar: Felsöka och felsöka .NET Server SDK
Azure App Service tillhandahåller flera fel söknings-och fel söknings tekniker för ASP.NET-program:

* [Övervaka en Azure App Service](../app-service/web-sites-monitor.md)
* [Aktivera diagnostisk loggning i Azure App Service](../app-service/troubleshoot-diagnostic-logs.md)
* [Felsöka ett Azure App Service i Visual Studio](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>Loggning
Du kan skriva till App Service diagnostikloggar med hjälp av standard skrivningen för ASP.NET-spårning. Innan du kan skriva till loggarna måste du aktivera diagnostik i Server delen för mobilappen.

Så här aktiverar du diagnostik och skrivning till loggarna:

1. Följ stegen i [Aktivera program loggning (Windows)](../app-service/troubleshoot-diagnostic-logs.md#enable-application-logging-windows).
2. Lägg till följande using-instruktion i din kod fil:

        using System.Web.Http.Tracing;
3. Skapa en spårnings skrivare för att skriva från .NET-Server delen till diagnostikloggar enligt följande:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. Publicera om ditt Server projekt och få åtkomst till Server delen för mobilappar för att köra kod Sök vägen med loggningen.
5. Hämta och utvärdera loggarna enligt beskrivningen i [Access-loggfiler](../app-service/troubleshoot-diagnostic-logs.md#access-log-files).

### <a name="local-debug"></a>Lokal fel sökning med autentisering
Du kan köra programmet lokalt för att testa ändringar innan du publicerar dem i molnet. För de flesta Azure Mobile Apps-arbetssidor trycker du på *F5* i Visual Studio. Det finns dock några ytterligare överväganden när du använder autentisering.

Du måste ha en molnbaserad mobilapp med App Service autentisering/auktorisering konfigurerad, och klienten måste ha moln slut punkten angiven som den alternativa inloggnings värden. Se dokumentationen för din klient plattform för de steg som krävs.

Se till att din mobila Server del har [Microsoft.Azure.Mobile.Server.Authentication] installerat. Lägg sedan till följande i programmets OWIN-startklass när `MobileAppConfiguration` har tillämpats på `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

I föregående exempel bör du konfigurera program inställningarna *authAudience* och *AuthIssuer* i din Web. config-fil till var och en som URL till program roten med hjälp av https-schemat. På samma sätt bör du ange *authSigningKey* som värde för programmets signerings nyckel.
Så här hämtar du signerings nyckeln:

1. Navigera till din app i [Azure Portal]
2. Klicka på **verktyg**, **kudu**, **Go**.
3. I kudu hanterings plats klickar du på **miljö**.
4. Hitta värdet för *webbplatsen @ no__t-1AUTH @ no__t-2SIGNING @ no__t-3KEY*.

Använd signerings nyckeln för parametern *authSigningKey* i din lokala program konfiguration.  Din mobil Server del är nu utrustad för att verifiera token när de körs lokalt, vilket innebär att-klienten hämtar token från den molnbaserade slut punkten.

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
