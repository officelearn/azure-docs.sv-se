<properties 
    pageTitle="Så här använder du Azure Redis Cache | Microsoft Azure" 
    description="Lär dig att förbättra prestandan för Azure-program med Azure Redis Cache" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# Så här använder du Azure Redis Cache

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Den här guiden visar hur du kommer igång med **Azure Redis Cache**. Microsoft Azure Redis Cache är baserad på den populära öppna källkoden Redis Cache. Den ger dig tillgång till en säker och dedikerad Redis-cache som hanteras av Microsoft. Ett cacheminne som har skapats med Azure Redis Cache är tillgängligt från alla program i Microsoft Azure.

Microsoft Azure Redis Cache finns tillgängligt på följande nivåer:

-   **Grundläggande** – En nod. Flera storlekar upp till 53 GB.
-   **Standard** – Primär/replik med två noder. Flera storlekar upp till 53 GB. 99,9 % SLA.
-   **Premium** – Primär/replik med två noder och upp till 10 shards. Flera storlekar från 6 GB till 530 GB (kontakta oss för mer information). Alla standardnivåfunktioner med mera, med stöd för [Redis-kluster](cache-how-to-premium-clustering.md), [Redis persistence](cache-how-to-premium-persistence.md) och [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9 % SLA.

Varje nivå har olika funktioner och priser. Mer information om priser finns i [Prissättning av cache][].

Den här guiden visar hur du använder klienten [StackExchange.Redis][] med hjälp av C\#-kod. Scenarier som omfattas är **att skapa och konfigurera en cache**, **konfigurera cacheklienter**, samt **att lägga till och ta bort objekt från cachen**. Mer information om hur du använder Azure Redis Cache finns i avsnittet [Nästa steg][]. En stegvis självstudie för hur man skapar en ASP.NET MVC-webbapp med Redis Cache finns i [Så här skapar du en webbapp med Redis Cache](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## Kom igång med Azure Redis Cache

Att komma igång med Azure Redis Cache är enkelt. Kom igång genom att etablera och konfigurera en cache. Därefter konfigurerar du cacheklienterna så att de får åtkomst till cacheminnet. När cacheklienterna är konfigurerade kan du börja använda dem.

-   [Skapa cachen][]
-   [Konfigurera cacheklienterna][]

<a name="create-cache"></a>
## Skapa en cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### Så här får du åtkomst till din cache när den har skapats

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Mer information om hur du konfigurerar din cache finns i [Så här konfigurerar du Azure Redis Cache](cache-configure.md).

<a name="NuGet"></a>
## Konfigurera cacheklienterna

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

När klientprojektet är konfigurerat för cachelagring kan du använda de metoder som beskrivs i följande avsnitt för att arbeta med din cache.

<a name="working-with-caches"></a>
## Arbeta med cacheminnen

Stegen i det här avsnittet beskriver hur du utför vanliga uppgifter med cachen.

-   [Ansluta till cachen][]
-   [Lägg till och hämta objekt från cachen][]
-   [Arbeta med .NET-objekt i cachen](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Ansluta till cachen

För att kunna arbeta programmässigt med en cache måste du ha en referens till cachen. Lägg till följande överst i den fil där du vill använda StackExchange.Redis-klienten för att få åtkomst till en Azure Redis Cache.

    using StackExchange.Redis;

>[AZURE.NOTE] StackExchange.Redis-klienten kräver .NET Framework 4 eller senare.

Anslutningen till Azure Redis Cache hanteras av `ConnectionMultiplexer`-klassen. Den här klassen är utformad för att delas och återanvändas i hela klientprogrammet och behöver därför inte skapas vid varje åtgärd. 

För att ansluta till en Azure Redis Cache och returnera en instans av en ansluten `ConnectionMultiplexer`, anropar du den statiska `Connect`-metoden samt skickar cacheslutpunkten och nyckeln enligt följande exempel. Använd nyckeln som genererades från Azure Portal som lösenordsparameter.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Varning: Lagra aldrig autentiseringsuppgifterna i källkoden. För att exemplet ska vara enkelt att förstå, visar jag dem i källkoden. Se [Så här fungerar programsträngar och anslutningssträngar][] för information om hur du lagrar autentiseringsuppgifter.

Om du inte vill använda SSL, anger du antingen `ssl=false` eller utelämnar `ssl`-parametern.

>[AZURE.NOTE] Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Anvisningar om hur du aktiverar icke-SSL-porten finns i [Åtkomstportar](cache-configure.md#access-ports)...

En metod för att dela en `ConnectionMultiplexer`-instans i ditt program är att ha en statisk egenskap som returnerar en ansluten instans, ungefär som i följande exempel. Detta ger ett trådsäkert sätt att endast initiera en enda ansluten `ConnectionMultiplexer`-instans. I dessa exempel är `abortConnect` inställd på false, vilket innebär att anropet lyckas även om det inte finns någon anslutning till Azure Redis Cache. En viktig egenskap i `ConnectionMultiplexer` är att anslutningen återställs automatiskt till cachen när nätverksproblemet eller andra fel är lösta.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Mer information om avancerade alternativ för anslutningskonfiguration finns i [Konfigurationsmodell för StackExchange.Redis][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

När anslutningen har upprättats returneras en referens till Redis-cachedatabasen genom att anropa `ConnectionMultiplexer.GetDatabase`-metoden. Det objekt som returneras från `GetDatabase`-metoden är ett förenklat genomströmningsobjekt som inte behöver lagras.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Nu när du vet hur du ansluter till en Azure Redis Cache-instans och returnerar en referens till cachedatabasen, tar vi oss en titt på att arbeta med cacheminnet.

<a name="add-object"></a>
## Lägg till och hämta objekt från cachen

Objekt kan lagras i och hämtas från ett cacheminne med hjälp av `StringSet`- och `StringGet`-metoderna.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis lagrar de flesta data som Redis-strängar, men dessa strängar kan innehålla flera typer av data, inklusive serialiserade binära data som kan användas när .NET-objekt lagras i cacheminnet.

Om objektet finns vid anrop av `StringGet`, returneras det och om inte returneras `null`. I det här fallet kan du hämta värdet från den önskade datakällan och lagra det i cacheminnet för senare användning. Detta kallas för ett cache aside-mönster.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Ange förfallodatum för ett objekt i cacheminnet med hjälp av `TimeSpan`-parametern för `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Arbeta med .NET-objekt i cachen

Azure Redis Cache kan cachelagra både .NET-objekt och basdatatyper, men .NET-objekt måste serialiseras innan de kan cachelagras. Detta är programutvecklarens ansvar och ger utvecklaren flexibilitet i valet av serialiserare.

Ett enkelt sätt att serialisera objekt är att använda `JsonConvert`-serialiseringsmetoderna i [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) och serialisera till och från JSON. I följande exempel visas en hämtning och en inställning med hjälp av en `Employee`-objektinstans.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## Nästa steg

Nu när du har lärt dig grunderna kan du följa dessa länkar för att lära dig mer om Azure Redis Cache.

-   Lär dig mer om ASP.NET-providers för Azure Redis Cache.
    -   [Sessionstillståndsprovider för Azure Redis ](cache-aspnet-session-state-provider.md)
    -   [Utdatacacheprovider för Azure Redis Cache ASP.NET](cache-aspnet-output-cache-provider.md)
-   [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet. Du kan visa värdena i Azure Portal och du kan också [ladda ned och granska](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem med hjälp av de verktyg du väljer.
-   Lär dig mer om [Cacheklientdokumentation för StackExchange.Redis][].
    -   Azure Redis Cache kan nås från många Redis-klienter och programmeringsspråk. Mer information finns i [http://redis.io/clients][].
-   Azure Redis Cache kan också användas med tjänster och verktyg från tredje part som Redsmin och Redis Desktop Manager.
    -   Mer information om Redsmin finns i [Så här hämtar du en anslutningssträng för Azure Redis och använder den med Redsmin][].
    -   Komma åt och granska dina data i Azure Redis Cache med ett grafiskt användargränssnitt med hjälp av [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
-   Se [Redis][]-dokumentationen, läs om [Redis-datatyper][] och få [en 15 minuters introduktion till Redis-datatyper][].



<!-- INTRA-TOPIC LINKS -->
[Nästa steg]: #next-steps
[Introduktion till Azure Redis Cache (video)]: #video
[Vad är Azure Redis Cache?]: #what-is
[Skapa en Azure Cache]: #create-cache
[Vilken typ av cachelagring passar mig?]: #choosing-cache
[Förbered ditt Visual Studio-projekt till att använda Azures cachelagring]: #prepare-vs
[Konfigurera ditt program till att använda cachelagring]: #configure-app
[Kom igång med Azure Redis Cache]: #getting-started-cache-service
[Skapa cachen]: #create-cache
[Konfigurera cachen]: #enable-caching
[Konfigurera cacheklienterna]: #NuGet
[Arbeta med cacheminnen]: #working-with-caches
[Ansluta till cachen]: #connect-to-cache
[Lägg till och hämta objekt från cachen]: #add-object
[Ange förfallodatum för ett objekt i cachen]: #specify-expiration
[Lagra ASP.NET-sessionstillstånd i cachen]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Utveckla på andra språk för Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Så här hämtar du en anslutningssträng för Azure Redis och använder den med Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Sessionstillståndsprovider för Azure Redis ]: http://go.microsoft.com/fwlink/?LinkId=398249
[Så här gör du: Konfigurera en cacheklient programmässigt]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Sessionstillståndsprovider för Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric-cache: Cachelagra sessionstillstånd]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Utdatacacheprovider för Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure-delad cachelagring]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Teamblogg]: http://blogs.msdn.com/b/windowsazure/
[Azures cachelagring]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Så här konfigurerar du storlekar för virtuella datorer]: http://go.microsoft.com/fwlink/?LinkId=164387
[Aspekter vid kapacitetsplanering för Azures cachelagring]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azures cachelagring]: http://go.microsoft.com/fwlink/?LinkId=252658
[Så här gör du: Ange cachebarheten för en ASP.NET-sida deklarativt]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[Så här gör du: Ange en sidas cachebarhet programmässigt]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Konfigurera en cache i Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Konfigurationsmodell för StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Arbeta med .NET-objekt i cachen]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[Installation av NuGet-pakethanteraren]: http://go.microsoft.com/fwlink/?LinkId=240311
[Prissättning av cache]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Översikt över Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrera till Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Exempel på Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320840
[Hantera dina Azure-resurser med hjälp av resursgrupper]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Cacheklientdokumentation för StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis-datatyper]: http://redis.io/topics/data-types
[en 15 minuters introduktion till Redis-datatyper]: http://redis.io/topics/data-types-intro

[Så här fungerar programsträngar och anslutningssträngar]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/





<!--HONumber=sep16_HO1-->


