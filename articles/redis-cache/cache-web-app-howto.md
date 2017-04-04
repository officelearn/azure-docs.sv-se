---
title: "Så här skapar du en webbapp med Redis Cache | Microsoft Docs"
description: "Lär dig hur du skapar en webbapp med Redis Cache"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 02e30f7fcbe0782528460b542a75f1d11c7286a1
ms.lasthandoff: 03/28/2017


---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Så här skapar du en webbapp med Redis Cache
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

I den här självstudien får du veta hur du skapar och distribuerar en ASP.NET-webbapp till en webbapp i Azure App Service med hjälp av Visual Studio 2017. Exempelprogrammet som visar en lista med teamstatistik från en databas och olika sätt att använda Azure Redis Cache för att lagra och hämta data från cacheminnet. När du slutför självstudien har du en webbapp som läser och skriver till en databas, samt som är optimerad med Azure Redis Cache och värdbaserad i Azure.

Du får lära dig:

* Hur du skapar en webbapp med ASP.NET MVC 5 i Visual Studio.
* Hur du kommer åt data från en databas med Entity Framework.
* Hur du förbättrar dataflödet och minskar databasbelastningen genom att lagra och hämta data med Azure Redis Cache.
* Hur du använder en Redis-sorterad uppsättning till att hämta de översta 5 teamen.
* Så här etablerar du Azure-resurserna för programmet med en Resource Manager-mall.
* Hur du publicerar programmet till Azure med Visual Studio.

## <a name="prerequisites"></a>Krav
För att kunna slutföra den här självstudien behöver du följande.

* [Azure-konto](#azure-account)
* [Visual Studio 2017 med Azure SDK för .NET](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Azure-konto
Du behöver ett Azure-konto för att kunna slutföra den här självstudien. Du kan:

* [Öppna ett Azure-konto kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Du får kredit som kan användas för att prova Azure-tjänster som normalt inte är kostnadsfria. Du kan behålla kontot även efter att krediten är slut och använda kostnadsfria Azure-tjänster och -funktioner.
* [Aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 med Azure SDK för .NET
Självstudien gäller för Visual Studio 2017 med [Azure SDK för .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools). Azure SDK 2.9.5 medföljer installationsprogrammet för Visual Studio.

Om du har Visual Studio 2015 kan du följa självstudien med [Azure SDK for .NET](../dotnet-sdk.md) 2.8.2 eller senare. [Ladda ned den senaste Azure-SDK:n för Visual Studio 2015 här](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio installeras automatiskt med SDK:n om du inte redan har det. Vissa av skärmarna kan se annorlunda ut än i självstudien.

Om du har Visual Studio 2013 kan du [ladda ned den senaste Azure-SDK:n för Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Vissa av skärmarna kan se annorlunda ut än i självstudien.

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet
1. Öppna Visual Studio och klicka på **Arkiv**, **Nytt**, **Projekt**.
2. Expandera noden **Visual C#** i listan **Mallar**, välj **Moln** och klicka på **ASP.NET-webbapp**. Kontrollera att **.NET Framework 4.5.2** eller senare har valts.  Skriv **ContosoTeamStats** i textrutan **Namn** och klicka på **OK**.
   
    ![Skapa projekt][cache-create-project]
3. Välj **MVC** som projekttyp. 

    Kontrollera att **Ingen autentisering** är angivet i **autentiseringsinställningarna**. Beroende på din version av Visual Studio kan standard anges till något annat. För att ändra detta klickar du på **Ändra autentisering** och väljer **Ingen autentisering**.

    Om du använder Visual Studio 2015 avmarkerar du kryssrutan **Värd i molnet**. Du kommer att [etablera Azure-resurserna](#provision-the-azure-resources) och [publicera programmet till Azure](#publish-the-application-to-azure) i efterföljande steg i självstudien. Ett exempel på att etablera en App Service-webbapp från Visual Studio genom att låta **Värd i molnet** vara markerad finns i [Kom igång med Web Apps i Azure App Service med ASP.NET och Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).
   
    ![Välj projektmall][cache-select-template]
4. Klicka på **OK** för att skapa projektet.

## <a name="create-the-aspnet-mvc-application"></a>Skapa MVC-appen i ASP.NET
I det här avsnittet av självstudierna ska du skapa det grundläggande program som läser och visar teamstatistik från en databas.

* [Lägga till paketet Entity Framework NuGet](#add-the-entity-framework-nuget-package)
* [Lägg till modellen](#add-the-model)
* [Lägg till kontrollanten](#add-the-controller)
* [Konfigurera vyerna](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Lägga till paketet Entity Framework NuGet

1. Klicka på **NuGet Package Manager**, **Package Manager-konsolen** på menyn **Verktyg**.
2. Kör följande kommando från fönstret `Package Manager Console`.
    
    ```
    Install-Package EntityFramework
    ```

Mer information om det här paketet finns i NuGet-paketet [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-model"></a>Lägg till modellen
1. Högerklicka på **Modeller** i **Solution Explorer** och välj **Lägg till**, **Klass**. 
   
    ![Lägg till modell][cache-model-add-class]
2. Ange `Team` som klassnamn och klicka på **Lägg till**.
   
    ![Lägg till modellklass][cache-model-add-class-dialog]
3. Ersätt `using`-satserna överst i `Team.cs`-filen med följande `using`-satser.

    ```c#
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. Ersätt definitionen av `Team`-klassen med följande kodfragment som innehåller en uppdaterad `Team`-klassdefinition samt vissa andra Entity Framework-hjälpklasser. Mer information om den första kodmetoden för Entity Framework som används i den här självstudien finns i [Kod först till en ny databas](https://msdn.microsoft.com/data/jj193542).

    ```c#
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. Gå till **Solution Explorer** och dubbelklicka på **web.config** för att öppna det.
   
    ![Web.config][cache-web-config]
2. Lägg till följande avsnitt `connectionStrings`. Namnet på anslutningssträngen måste matcha namnet på klassen för Entity Framework-databasens kontext som är `TeamContext`.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Du kan lägga till det nya `connectionStrings` avsnittet så att det följer `configSections`, vilket visas i följande exempel.

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

### <a name="add-the-controller"></a>Lägg till kontrollanten
1. Tryck på **F6** för att skapa projektet. 
2. I **Solution Explorer** högerklickar du på mappen **Kontrollanter**. Klicka sedan på **Lägg till** och på **Kontrollant**.
   
    ![Lägga till en kontrollant][cache-add-controller]
3. Välj **MVC 5-kontrollant med vyer, med hjälp av Entity Framework** och klicka på **Lägg till**. Om du får ett fel när du klickar på **Lägg till**, kontrollerar du att du har skapat projektet först.
   
    ![Lägga till en kontrollantklass][cache-add-controller-class]
4. Välj **Team (ContosoTeamStats.Models)** från listrutan **Modellklass**. Välj **TeamContext (ContosoTeamStats.Models)** från listrutan **Datakontextklass**. Skriv `TeamsController` i textrutan **Kontrollant** (om den inte automatiskt har fyllts i). Klicka på **Lägg till** för att skapa kontrollantklassen och lägga till standardvyerna.
   
    ![Konfigurera kontrollanten][cache-configure-controller]
5. I **Solution Explorer** expanderar du **Global.asax** och dubbelklickar på **Global.asax.cs** för att öppna den.
   
    ![Global.asax.cs][cache-global-asax]
6. Lägg till följande två `using`-satser längst upp i filen under de andra `using`-satserna.

    ```c#
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. Lägg till följande kodrad i slutet av `Application_Start`-metoden.

    ```c#
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. I **Solution Explorer** expanderar du `App_Start` och dubbelklickar på `RouteConfig.cs`.
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. Ersätt `controller = "Home"` i följande kod i `RegisterRoutes`-metoden med `controller = "Teams"` som visas i följande exempel.

    ```c#
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>Konfigurera vyerna
1. I **Solution Explorer** expanderar du mappen **Vyer** och sedan mappen **Delad**. Dubbelklicka på **_Layout.cshtml**. 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. Ändra innehållet i `title`-elementet och ersätt `My ASP.NET Application` med `Contoso Team Stats` som visas i följande exempel.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. I avsnittet `body` uppdaterar du den första `Html.ActionLink`-satsen samt ersätter `Application name` med `Contoso Team Stats` och `Home` med `Teams`.
   
   * Innan: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * Efter: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![Kodändringar][cache-layout-cshtml-code]
2. Tryck på **Ctrl+F5** för att skapa och köra programmet. Den här versionen av programmet läser resultaten direkt från databasen. Observera att åtgärderna **Skapa nytt**, **Redigera**, **Information** och **Ta bort** har lagts till automatiskt till programmet av **MVC 5-kontrollanten med vyer, med hjälp av Entity Framework**-autogenerering. I nästa avsnitt i självstudien lägger du till Redis Cache för att optimera dataåtkomst och ange ytterligare funktioner i programmet.

![Startprogram][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Konfigurera programmet till att använda Redis Cache
I det här avsnittet av självstudierna konfigurerar du exempelprogrammet till att lagra och hämta Contoso-teamets statistik från en Azure Redis Cache-instans med hjälp av [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)-cacheklienten.

* [Konfigurera programmet till att använda StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Uppdatera TeamsController-klassen till att returnera resultat från cachen eller databasen](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [Uppdatera metoderna Skapa, Redigera och Ta bort till att arbeta med cacheminnet](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [Uppdatera vyn Teamindex till att arbeta med cacheminnet](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Konfigurera programmet till att använda StackExchange.Redis
1. Om du vill konfigurera ett klientprogram i Visual Studio med hjälp av NuGet-paketet för StackExchange.Redis klickar du på projektet i **NuGet Package Manager** och väljer **Package Manager-konsolen** på menyn **Verktyg**.
2. Kör följande kommando från fönstret `Package Manager Console`.
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    NuGet-paketet hämtar och lägger till de nödvändiga sammansättningsreferenserna för klientprogrammet för att få åtkomst till Azure Redis Cache med cacheklienten StackExchange.Redis. Om du vill använda en starkt krypterad version av `StackExchange.Redis` klientbiblioteket ska du installera paketet `StackExchange.Redis.StrongName`.
3. I **Solution Explorer** expanderar du mappen **Kontrollanter** och sedan dubbelklickar du på filen **TeamController.cs** för att öppna den.
   
    ![Teamkontrollant][cache-teamscontroller]
4. Lägg till följande två `using`-satser i **TeamsController.cs**.

    ```c#   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. Lägg till följande två egenskaper i `TeamsController`-klassen.

    ```c#   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. Skapa en fil på datorn med namnet `WebAppPlusCacheAppSecrets.config` och placera den på en plats som inte checkas in med källkoden för exempelprogrammet, om du väljer att checka in den någon annanstans. I det här exemplet finns `AppSettingsSecrets.config`-filen i `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.
   
    Redigera `WebAppPlusCacheAppSecrets.config`-filen och lägg till följande innehåll. Om du kör programmet lokalt används den här informationen till att ansluta till din Azure Redis Cache-instans. Senare i självstudien etablerar du en Azure Redis Cache-instans och uppdaterar cachens namn och lösenord. Om du inte tänker köra exempelprogrammet lokalt kan du hoppa över skapandet av den här filen och efterföljande steg som refererar till filen, eftersom när du distribuerar till Azure hämtar programmet cacheanslutningsinformation från appinställningen för webbappen och inte från den här filen. Eftersom `WebAppPlusCacheAppSecrets.config` inte har distribuerats till Azure med ditt program, behöver du inte det, såvida du inte tänker köra programmet lokalt.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. Gå till **Solution Explorer** och dubbelklicka på **web.config** för att öppna det.
   
    ![Web.config][cache-web-config]
2. Lägg till följande `file`-attribut i `appSettings`-elementet. Om du använder ett annat namn eller en annan plats, byter du ut dessa värden mot de som visas i exemplet.
   
   * Innan: `<appSettings>`
   * Efter: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   ASP.NET-körningsmiljön sammanfogar innehållet i den externa filen med markeringen i `<appSettings>`-elementet. Körningen ignorerar filattributet om det inte går att hitta den angivna filen. Din hemliga information (anslutningssträngen till cachen) ingår inte i källkoden för programmet. När du distribuerar din webbapp till Azure kommer `WebAppPlusCacheAppSecrests.config`-filen inte att distribueras (om det är det du vill). Det finns flera sätt att ange dessa hemligheter i Azure, och i den här självstudien konfigureras de automatiskt åt dig när du [etablerar Azure-resurserna](#provision-the-azure-resources) i ett senare skede av självstudierna. Mer information om hur du arbetar med hemligheter i Azure finns i [Metodtips för att distribuera lösenord och andra känsliga data till ASP.NET och Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Uppdatera TeamsController-klassen till att returnera resultat från cachen eller databasen
I det här exemplet kan teamstatistik hämtas från databasen eller från cachen. Teamstatistik lagras i cacheminnet som en serialiserad `List<Team>` och även som en sorterad uppsättning med Redis-datatyper. När du hämtar objekt från en sorterad uppsättning hämtar du vissa, alla eller frågar efter vissa objekt. I det här exemplet får du fråga i den sorterade uppsättningen efter de översta 5 team som rangordnas efter antalet WINS.

> [!NOTE]
> Du behöver inte spara teamstatistiken i olika format i cacheminnet för att kunna använda Azure Redis Cache. Den här självstudien använder flera olika format för att visa några av de olika sätt och olika datatyper som du kan använda för att cachelagra data.
> 
> 

1. Lägg till följande två `using`-satser längst upp i `TeamsController.cs`-filen tillsammans med de andra `using`-satserna.

    ```c#   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. Ersätt den befintliga `public ActionResult Index()`-metodimplementeringen med följande implementering.

    ```c#
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. Lägg till följande tre metoder till `TeamsController`-klassen för att implementera åtgärdstyperna `playGames`, `clearCache` och `rebuildDB` från switch-satsen som lades till i föregående kodfragment.
   
    `PlayGames`-metoden uppdaterar teamstatistiken genom att simulera en spelsäsong, spara resultaten i databasen och rensa inaktuella data från cachen.

    ```c#
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `RebuildDB`-metoden återinitierar databasen med en standarduppsättning av team, genererar statistik för dem och rensar inaktuella data från cachen.

    ```c#
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `ClearCachedTeams`-metoden tar bort cachelagrad teamstatistik från cachen.

    ```c#
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. Lägg till följande fyra metoder till `TeamsController`-klassen för att implementera olika sätt att hämta teamstatistik från cacheminnet och databasen. Var och en av dessa metoder returnerar en `List<Team>` som visas i vyn.
   
    `GetFromDB`-metoden läser teamstatistiken från databasen.
   
    ```c#
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    `GetFromList`-metoden läser teamstatistiken från cachen som en serialiserad `List<Team>`. Om det finns en cachemiss, läses teamstatistiken från databasen och lagras sedan i cacheminnet till nästa gång. I det här exemplet använder vi JSON.NET-serialisering för att serialisera .NET-objekt till och från cacheminnet. Mer information finns i [Så här arbetar du med .NET-objekt i Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```c#
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    `GetFromSortedSet`-metoden läser teamstatistiken från en cachelagrad sorterad uppsättning. Om det finns en cachemiss, läses teamstatistiken från databasen och lagras i cacheminnet som en sorterad uppsättning.

    ```c#
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    `GetFromSortedSetTop5`-metoden läser de översta 5 teamen från den cachelagrade sorterade uppsättningen. Den startar genom att kontrollera cacheminnet efter förekomsten av `teamsSortedSet`-nyckeln. Om den här nyckeln inte finns, anropas `GetFromSortedSet`-metoden för att läsa teamstatistiken och lagra den i cacheminnet. Därefter efterfrågas den cachelagrade sorterade uppsättningens översta 5 team som returneras.

    ```c#
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Uppdatera metoderna Skapa, Redigera och Ta bort till att arbeta med cacheminnet
Den autogenererade kod som skapats som en del av det här exemplet innehåller metoder för att lägga till, redigera och ta bort team. Varje gång ett team läggs till, redigeras eller tas bort, blir data i cacheminnet inaktuell. I det här avsnittet ska du ändra dessa tre metoder för att rensa cachelagrade team så att cachen inte är synkroniserad med databasen.

1. Bläddra till `Create(Team team)`-metoden i `TeamsController`-klassen. Lägg till ett anrop till `ClearCachedTeams`-metoden enligt följande exempel.

    ```c#
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. Bläddra till `Edit(Team team)`-metoden i `TeamsController`-klassen. Lägg till ett anrop till `ClearCachedTeams`-metoden enligt följande exempel.

    ```c#
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. Bläddra till `DeleteConfirmed(int id)`-metoden i `TeamsController`-klassen. Lägg till ett anrop till `ClearCachedTeams`-metoden enligt följande exempel.

    ```c#
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Uppdatera vyn Teamindex till att arbeta med cacheminnet
1. I **Solution Explorer** expanderar du mappen **Vyer** och sedan mappen **Team**. Dubbelklicka på **Index.cshtml**.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. Leta efter följande paragrafelement i den övre delen av filen.
   
    ![Åtgärdstabell][cache-teams-index-table]
   
    Detta är länken för att skapa ett nytt team. Ersätt paragrafelementet med följande tabell. Den här tabellen innehåller åtgärdslänkar för att skapa ett nytt team, spela en ny spelsäsong, rensa cacheminnet, hämta teamen från cachen i olika format, hämta teamen från databasen och återskapa databasen med ny exempeldata.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. Bläddra till slutet av filen **Index.cshtml** och lägg till följande `tr`-element så att det blir den sista raden i den sista tabellen i filen.
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    Den här raden visar värdet för `ViewBag.Msg` som innehåller en statusrapport om den aktuella åtgärden. `ViewBag.Msg` ställs in när du klickar på någon av åtgärdslänkarna i föregående steg.   
   
    ![Statusmeddelande][cache-status-message]
2. Tryck på **F6** för att skapa projektet.

## <a name="provision-the-azure-resources"></a>Etablera Azure-resurserna
För att vara värd för programmet i Azure, måste du först etablera Azure-tjänster som programmet behöver. Exempelprogrammet i den här självstudien använder följande Azure-tjänster.

* Azure Redis Cache
* App Service-webbapp
* SQL Database

Om du vill distribuera dessa tjänster till en ny eller befintlig resursgrupp klickar du på knappen **Distribuera till Azure**.

[![Deploy to Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Knappen **Distribuera till Azure** använder mallen [Skapa en webbapp plus Redis Cache plus SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure Quickstart](https://github.com/Azure/azure-quickstart-templates) för att etablera dessa tjänster samt ange anslutningssträngen för SQL Database och programinställningen för anslutningssträngen i Azure Redis Cache.

> [!NOTE]
> Om du inte har något Azure-konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) på bara några minuter.
> 
> 

När du klickar på knappen **Distribuera till Azure** går du till Azure Portal och initierar processen att skapa de resurser som beskrivs i mallen.

![Distribuera till Azure][cache-deploy-to-azure-step-1]

1. I avsnittet **Grundläggande** väljer du vilken Azure-prenumerationen som ska användas. Välj också en befintlig resursgrupp eller skapa en ny och ange plats för resursgruppen.
2. I avsnittet **Inställningar** anger du **inloggningsuppgifter för administratörer** (använd inte **admin**), **ett lösenord för administratörer** och ett **databasnamn**. De andra parametrarna har konfigurerats för en kostnadsfri App Service-värdplan och lägre kostnadsalternativ för SQL Database och Azure Redis Cache, vilka saknar en kostnadsfri nivå.

    ![Distribuera till Azure][cache-deploy-to-azure-step-2]

3. Bläddra till slutet av sidan när du har konfigurerat de önskade inställningarna, läs villkoren och markera kryssrutan **Jag godkänner villkoren ovan**.
4. För att påbörja etablering av resurserna klickar du på **Köp**.

Visa förloppet för distributionen genom att klicka på aviseringsikonen och **Distributionen har påbörjats**.

![Distributionen har påbörjats][cache-deployment-started]

Du kan visa statusen för din distribution på bladet **Microsoft.Template**.

![Distribuera till Azure][cache-deploy-to-azure-step-3]

När etableringen har slutförts kan du publicera programmet till Azure från Visual Studio.

> [!NOTE]
> Eventuella fel som kan uppstå under etableringen visas på bladet **Microsoft.Template**. Vanliga fel är för många SQL-servrar eller för många värdplaner för den kostnadsfria apptjänsten per prenumeration. Lös eventuella fel och starta om processen genom att klicka på **Distribuera om** på bladet **Microsoft.Template** eller knappen **Distribuera till Azure** i den här självstudien.
> 
> 

## <a name="publish-the-application-to-azure"></a>Publicera programmet till Azure
I det här steget i självstudien publicerar du programmet till Azure och kör det i molnet.

1. Högerklicka på projektet **ContosoTeamStats** i Visual Studio och välj **Publicera**.
   
    ![Publicera][cache-publish-app]
2. Klicka på **Microsoft Azure App Service**, välj **Välj befintlig** och klicka på **Publicera**.
   
    ![Publicera][cache-publish-to-app-service]
3. Välj den prenumeration som användes när du skapade Azure-resurserna och expandera resursgruppen som innehåller resurserna. Välj önskad webbapp. Om du använde knappen **Distribuera till Azure** börjar webbappnamnet med **webSite** följt av vissa ytterligare tecken.
   
    ![Välj webbapp][cache-select-web-app]
4. Klicka på **OK** för att påbörja publiceringsprocessen. Efter en liten stund slutförs publiceringsprocessen och en webbläsare startas med det exempelprogram som körs. Om du får ett DNS-fel vid validering eller publicering och etableringen för Azure-resurser för programmet nyligen har slutförts, väntar du en stund och försöker igen.
   
    ![Cachen har lagts till][cache-added-to-application]

I följande tabell beskrivs varje åtgärdslänk från exempelprogrammet.

| Åtgärd | Beskrivning |
| --- | --- |
| Skapa ny |Skapa ett nytt team. |
| Spela säsong |Spela en spelsäsong, uppdatera teamstatistiken och avmarkera inaktuell teamdata från cachen. |
| Rensa cacheminne |Rensa teamstatistik från cachen. |
| Lista från cache |Hämta teamstatistik från cachen. Om det finns en cachemiss läser du in statistiken från databasen och sparar den till cachen till nästa gång. |
| Sorterad uppsättning från cachen |Hämtar teamstatistiken från cachen med en sorterad uppsättning. Om det finns en cachemiss läser du in statistiken från databasen och sparar den till cachen med en sorterad uppsättning. |
| Översta 5 teamen från cachen |Hämtar de översta fem teamen från cachen med en sorterad uppsättning. Om det finns en cachemiss läser du in statistiken från databasen och sparar den till cachen med en sorterad uppsättning. |
| Läsa in från databas |Hämtar teamstatistiken från databasen. |
| Återskapa databas |Återskapa databasen och öppna den igen med exempelteamdata. |
| Redigera/Information/Ta bort |Redigera ett team, visa information om ett team, ta bort ett team. |

Klicka på några åtgärder och experimentera med att hämta data från olika källor. Observera skillnaderna mellan den tid det tar för att slutföra de olika sätten att hämta data från databasen och cachen.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Ta bort resurserna när du är klar med programmet
När du är klar med självstudiens exempelprogram kan du ta bort Azure-resurserna som användes för att spara kostnad och resurser. Om du använde knappen **Distribuera till Azure** i avsnittet [Etablera Azure-resurserna](#provision-the-azure-resources) och alla resurser finns i samma resursgrupp, kan du ta bort dem tillsammans i en enda åtgärd genom att ta bort resursgruppen.

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.
2. Skriv namnet på din resursgrupp i textrutan **Filtrera objekt...**.
3. Klicka på **...** till höger om din resursgrupp.
4. Klicka på **Ta bort**.
   
    ![Ta bort][cache-delete-resource-group]
5. Skriv namnet på din resursgrupp och klicka på **Ta bort**.
   
    ![Bekräfta borttagning][cache-delete-confirm]

Efter en liten stund tas resursgruppen och resurser som finns i den bort.

> [!IMPORTANT]
> Observera att det inte går att ångra borttagningen av en resursgrupp och att resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp kan du ta bort varje resurs separat från deras respektive blad.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>Kör exempelprogrammet på den lokala datorn
För att kunna köra programmet lokalt på datorn måste du ha en Azure Redis Cache-instans där du kan cachelagra data. 

* Om du har publicerat ditt program till Azure, enligt beskrivningen i föregående avsnitt, kan du använda Azure Redis Cache-instansen som etablerades under det steget.
* Om du har en annan befintlig Azure Redis Cache-instans kan du använda den till att köra det här exemplet lokalt.
* Om du behöver skapa en Azure Redis Cache-instans kan du följa stegen i [Skapa en cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

När du har valt eller skapat cacheminnet du ska använda, bläddrar du till cachen i Azure Portal och hämtar [värdnamnet](cache-configure.md#properties) samt [åtkomstnycklar](cache-configure.md#access-keys) för cacheminnet. Instruktioner finns i [Konfigurera Redis-cacheinställningarna](cache-configure.md#configure-redis-cache-settings).

1. Öppna den `WebAppPlusCacheAppSecrets.config`-fil som du skapade under steget [Konfigurera programmet till att använda Redis Cache](#configure-the-application-to-use-redis-cache) i den här självstudien med valfri redigerare.
2. Redigera `value`-attributet och ersätt `MyCache.redis.cache.windows.net` med [värdnamnet](cache-configure.md#properties) för cachen. Ange antingen den [primära eller sekundära nyckeln](cache-configure.md#access-keys) i cacheminnet som lösenord.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. Tryck på **Ctrl+F5** för att köra programmet.

> [!NOTE]
> Observera att eftersom programmet och databasen körs lokalt och Redis Cache finns i Azure, kan cacheminnet verka underprestera i databasen. För bästa prestanda bör klientprogrammet och Azure Redis Cache-instansen finnas på samma plats. 
> 
> 

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Komma igång med ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) på [ASP.NET](http://asp.net/)-webbplatsen.
* Fler exempel på hur du skapar en ASP.NET-webbapp i App Service finns i [Skapa och distribuera en ASP.NET-webbapp i Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) i [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz), 2015 års [anslutningsdemo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
  * Fler snabbstartsguider från HealthClinic.biz-demonstrationen finns i [Snabbstartsguider för Azure Developer Tools](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
* Läs mer om [Kod först till en ny databas](https://msdn.microsoft.com/data/jj193542) för Entity Framework som används i den här självstudien.
* Läs mer om [webbappar i Azure App Service](../app-service-web/app-service-web-overview.md).
* Lär dig hur du [övervakar](cache-how-to-monitor.md) cacheminnet i Azure Portal.
* Utforska Azure Redis Caches premiumfunktioner
  
  * [Så här konfigurerar du persistence för Premium Azure Redis Cache](cache-how-to-premium-persistence.md)
  * [Så här konfigurerar du klustring för Premium Azure Redis Cache](cache-how-to-premium-clustering.md)
  * [Så här konfigurerar du Virtual Network-stöd för Premium Azure Redis Cache](cache-how-to-premium-vnet.md)
  * Se [Vanliga frågor och svar om Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) för mer information om storlek, dataflöde och bandbredd med premium-cacheminnen.

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png


