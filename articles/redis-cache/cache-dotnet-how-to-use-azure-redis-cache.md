---
title: "Så här använder du Azure Redis Cache | Microsoft Docs"
description: "Lär dig att förbättra prestandan för Azure-program med Azure Redis Cache"
services: redis-cache,app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/27/2017
ms.author: sdanie
ms.openlocfilehash: 3dfc026490093523446650c510dbebdd660e8b6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-redis-cache"></a>Så här använder du Azure Redis Cache
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Den här guiden visar hur du kommer igång med **Azure Redis Cache**. Microsoft Azure Redis Cache är baserad på den populära öppna källkoden Redis Cache. Den ger dig tillgång till en säker och dedikerad Redis-cache som hanteras av Microsoft. Ett cacheminne som har skapats med Azure Redis Cache är tillgängligt från alla program i Microsoft Azure.

Microsoft Azure Redis Cache finns tillgängligt på följande nivåer:

* **Grundläggande** – En nod. Flera storlekar upp till 53 GB.
* **Standard** – Primär/replik med två noder. Flera storlekar upp till 53 GB. 99,9 % SLA.
* **Premium** – Primär/replik med två noder och upp till 10 shards. Flera storlekar från 6 GB till 530 GB. Alla standardnivåfunktioner med mera, med stöd för [Redis-kluster](cache-how-to-premium-clustering.md), [Redis persistence](cache-how-to-premium-persistence.md) och [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9 % SLA.

Varje nivå har olika funktioner och priser. Mer information om priser finns i [prisinformationen för Cache][Cache Pricing Details].

Den här guiden beskriver hur du använder [StackExchange.Redis][StackExchange.Redis]-klienten med hjälp av C\#-kod. Scenarier som omfattas är **att skapa och konfigurera en cache**, **konfigurera cacheklienter**, samt **att lägga till och ta bort objekt från cachen**. Mer information om hur du använder Azure Redis Cache finns i [Nästa steg][Next Steps]. En stegvis självstudie för hur man skapar en ASP.NET MVC-webbapp med Redis Cache finns i [Så här skapar du en webbapp med Redis Cache](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>

## <a name="get-started-with-azure-redis-cache"></a>Kom igång med Azure Redis Cache
Att komma igång med Azure Redis Cache är enkelt. Kom igång genom att etablera och konfigurera en cache. Därefter konfigurerar du cacheklienterna så att de får åtkomst till cacheminnet. När cacheklienterna är konfigurerade kan du börja använda dem.

* [Skapa cachen][Create the cache]
* [Konfigurera cacheklienterna][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Skapa en cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Så här får du åtkomst till din cache när den har skapats
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Mer information om hur du konfigurerar din cache finns i [Så här konfigurerar du Azure Redis Cache](cache-configure.md).

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Konfigurera cacheklienterna
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

När klientprojektet är konfigurerat för cachelagring kan du använda de metoder som beskrivs i följande avsnitt för att arbeta med din cache.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Arbeta med cacheminnen
Stegen i det här avsnittet beskriver hur du utför vanliga uppgifter med cachen.

* [Ansluta till cachen][Connect to the cache]
* [Lägga till och hämta objekt från cachen][Add and retrieve objects from the cache]
* [Arbeta med .NET-objekt i cachen](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>

## <a name="connect-to-the-cache"></a>Ansluta till cachen
För att kunna hantera en cache via programmering behöver du en referens till cachen. Lägg till följande överst i den fil där du vill använda StackExchange.Redis-klienten för att få åtkomst till en Azure Redis Cache.

    using StackExchange.Redis;

> [!NOTE]
> StackExchange.Redis-klienten kräver .NET Framework 4 eller senare.
> 
> 

Anslutningen till Azure Redis Cache hanteras av `ConnectionMultiplexer`-klassen. Den här klassen bör delas och återanvändas i hela klientprogrammet och behöver därför inte skapas för varje åtgärd. 

För att ansluta till en cache i Azure Redis Cache och returnera en instans av en ansluten `ConnectionMultiplexer` anropar du den statiska `Connect`-metoden och lägger till cacheslutpunkten och nyckeln. Använd nyckeln som genererades från Azure-portalen som lösenordsparameter.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

> [!IMPORTANT]
> Varning: Lagra aldrig autentiseringsuppgifterna i källkoden. För att exemplet ska vara enkelt att förstå, visar jag dem i källkoden. Se [Så här fungerar programsträngar och anslutningssträngar][How Application Strings and Connection Strings Work] för information om hur du lagrar autentiseringsuppgifter.
> 
> 

Om du inte vill använda SSL, anger du antingen `ssl=false` eller utelämnar `ssl`-parametern.

> [!NOTE]
> Observera att icke-SSL-porten är inaktiverad som standard för nya cacheminnen. Anvisningar för hur du aktiverar icke-SSL-porten finns i [Åtkomstportar](cache-configure.md#access-ports).
> 
> 

En metod för att dela en `ConnectionMultiplexer`-instans i ditt program är att ha en statisk egenskap som returnerar en ansluten instans, ungefär som i följande exempel. Det här tillvägagångssättet är ett trådsäkert sätt för att endast initiera en enskild ansluten `ConnectionMultiplexer`-instans. I dessa exempel är `abortConnect` inställt på false, vilket innebär att anropet lyckas även om det inte finns någon anslutning till Azure Redis Cache. En viktig egenskap i `ConnectionMultiplexer` är att anslutningen till cachen återställs automatiskt när nätverksproblemet eller andra fel har åtgärdats.

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

Mer information om avancerade alternativ för anslutningskonfiguration finns i [Konfigurationsmodell för StackExchange.Redis][StackExchange.Redis configuration model].

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

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

Azure Redis-cacheminnen har ett konfigurerbart antal databaser (16 är standard) som kan användas för att logiskt separera data i ett Redis-cacheminne. Mer information finns i [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Vad är Redis-databaser?) och [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Standardkonfiguration av Redis-server).

Nu när du vet hur du ansluter till en Azure Redis Cache-instans och returnerar en referens till cachedatabasen ska vi titta på hur du arbetar med cacheminnet.

<a name="add-object"></a>

## <a name="add-and-retrieve-objects-from-the-cache"></a>Lägg till och hämta objekt från cachen
Objekt kan lagras i och hämtas från ett cacheminne med hjälp av `StringSet`- och `StringGet`-metoderna.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis lagrar de flesta data som Redis-strängar, men dessa strängar kan innehålla flera typer av data, inklusive serialiserade binära data som kan användas när .NET-objekt lagras i cacheminnet.

Om objektet finns vid anrop av `StringGet`, returneras det och om inte returneras `null`. Om `null` returneras kan du hämta värdet från önskad datakälla och lagra det i cacheminnet för senare användning. Det här användningsmönstret kallas för cache aside-mönstret.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Du kan också använda `RedisValue`, som du ser i följande exempel. `RedisValue` har implicita operatorer för att hantera integrala datatyper och kan vara användbart om `null` är ett förväntat värde för ett cachelagrat objekt.


    RedisValue value = cache.StringGet("key1");
    if (!value.HasValue)
    {
        value = GetValueFromDataSource();
        cache.StringSet("key1", value);
    }


Ange förfallodatum för ett objekt i cacheminnet med hjälp av `TimeSpan`-parametern för `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Arbeta med .NET-objekt i cachen
Azure Redis Cache kan cachelagra både .NET-objekt och basdatatyper, men .NET-objekt måste serialiseras innan de kan cachelagras. Den här .NET-objektserialiseringen är programutvecklarens ansvar och ger utvecklaren flexibilitet i valet av serialiserare.

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

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna kan du följa dessa länkar för att lära dig mer om Azure Redis Cache.

* Lär dig mer om ASP.NET-providers för Azure Redis Cache.
  * [Sessionstillståndsprovider för Azure Redis](cache-aspnet-session-state-provider.md)
  * [Utdatacacheprovider för Azure Redis Cache ASP.NET](cache-aspnet-output-cache-provider.md)
* [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet. Du kan visa värdena på Azure-portalen eller [hämta och granska](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem med valfritt verktyg.
* Mer information finns i [dokumentationen för StackExchange.Redis-cacheklienter][StackExchange.Redis cache client documentation].
  * Azure Redis Cache kan nås från många Redis-klienter och programmeringsspråk. Mer information finns på [http://redis.io/clients][http://redis.io/clients].
* Azure Redis Cache kan också användas med tjänster och verktyg från tredje part som Redsmin och Redis Desktop Manager.
  * Mer information om Redsmin finns i [How to retrieve an Azure Redis connection string and use it with Redsmin][How to retrieve an Azure Redis connection string and use it with Redsmin] (Hämta en Azure Redis-anslutningssträng och använda den med Redsmin).
  * Komma åt och granska dina data i Azure Redis Cache med ett grafiskt användargränssnitt med hjälp av [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
* Se [Redis][redis]-dokumentationen, läs om [Redis-datatyper][redis data types] och [få en 15 minuters introduktion till Redis-datatyper][a fifteen minute introduction to Redis data types].

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[Connect to the cache]: #connect-to-cache
[Add and retrieve objects from the cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session


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
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[How to retrieve an Azure Redis connection string and use it with Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis configuration model]: https://stackexchange.github.io/StackExchange.Redis/Configuration

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Azure portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis cache client documentation]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis data types]: http://redis.io/topics/data-types
[a fifteen minute introduction to Redis data types]: http://redis.io/topics/data-types-intro

[How Application Strings and Connection Strings Work]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


