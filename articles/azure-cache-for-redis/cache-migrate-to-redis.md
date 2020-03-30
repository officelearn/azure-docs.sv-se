---
title: Migrera program för hanterade cachetjänster till Redis - Azure
description: Lär dig hur du migrerar hanterade cachetjänst- och cacheprogram i rollen till Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122680"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrera från Managed Cache Service till Azure Cache for Redis
Migrera dina program som använder Azure Managed Cache Service till Azure Cache för Redis kan utföras med minimala ändringar i ditt program, beroende på de hanterade cachetjänstfunktionerna som används av cacheprogrammet. Medan API:erna inte är exakt desamma är de likartade, och mycket av din befintliga kod som använder hanterad cachetjänst för att komma åt en cache kan återanvändas med minimala ändringar. Den här artikeln visar hur du gör nödvändiga konfigurations- och programändringar för att migrera dina managed cache service-program för att använda Azure Cache för Redis, och visar hur vissa av funktionerna i Azure Cache för Redis kan användas för att implementera funktionerna i en cache för hanterad cachetjänst för hanterad cache.

>[!NOTE]
>Managed Cache Service och In-Role Cache [drogs tillbaka](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) den 30 november 2016. Om du har några rollcachen-distributioner som du vill migrera till Azure Cache för Redis kan du följa stegen i den här artikeln.

## <a name="migration-steps"></a>Migreringssteg
Följande steg krävs för att migrera ett program för hanterad cachetjänst för att använda Azure Cache för Redis.

* Kartförvaltade cachetjänstfunktioner till Azure Cache för Redis
* Välj ett cacheerbjudande
* Skapa en cache
* Konfigurera cacheklienterna
  * Ta bort konfigurationen för hanterade cachetjänster
  * Konfigurera en cacheklient med StackExchange.Redis NuGet-paketet
* Migrera tjänstkod för hanterad cache
  * Ansluta till cachen med klassen ConnectionMultiplexer
  * Komma åt primitiva datatyper i cacheminnet
  * Arbeta med .NET-objekt i cachen
* Migrera ASP.NET sessionstillstånd och cachelagring av utdata till Azure Cache för Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Kartförvaltade cachetjänstfunktioner till Azure Cache för Redis
Azure Managed Cache Service och Azure Cache för Redis är liknande men implementerar några av deras funktioner på olika sätt. I det här avsnittet beskrivs några av skillnaderna och vägledning om hur du implementerar funktionerna i Hanterad cachetjänst i Azure Cache för Redis.

| Funktionen Hanterad cachetjänst | Support för hanterad cachetjänst | Support för Azure Cache för Redis |
| --- | --- | --- |
| Namngivna cacheminnen |En standardcache är konfigurerad och i standard- och Premium-cacheerbjudandena kan upp till nio ytterligare namngivna cacheminnen konfigureras om så önskas. |Azure Cache för Redis har ett konfigurerbart antal databaser (standard 16) som kan användas för att implementera en liknande funktionalitet som namngivna cacheminnen. Mer information finns i [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Vad är Redis-databaser?) och [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Standardkonfiguration av Redis-server). |
| Hög tillgänglighet |Ger hög tillgänglighet för objekt i cacheminnet i standard- och premiumcacheerbjudandena. Om objekt går förlorade på grund av ett fel är säkerhetskopior av objekten i cacheminnet fortfarande tillgängliga. Skrivningar till den sekundära cachen görs synkront. |Hög tillgänglighet är tillgänglig i standard- och premiumcacheerbjudandena, som har en primär konfiguration för två noder (varje shard i en Premium-cache har ett primärt/replikpar). Skrivningar till repliken görs asynkront. Mer information finns i [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/). |
| Meddelanden |Gör att klienter kan ta emot asynkrona meddelanden när en mängd olika cacheåtgärder inträffar på en namngiven cache. |Klientprogram kan använda Redis pub/sub- eller [Keyspace-meddelanden](cache-configure.md#keyspace-notifications-advanced-settings) för att uppnå liknande funktioner som meddelanden. |
| Lokal cache |Lagrar en kopia av cachelagrade objekt lokalt på klienten för extra snabb åtkomst. |Klientprogram skulle behöva implementera den här funktionen med hjälp av en ordlista eller liknande datastruktur. |
| Policy för vräkning |Ingen eller LRU. Standardprincipen är LRU. |Azure Cache för Redis stöder följande vräkningsprinciper: volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-tl, noeviction. Standardprincipen är flyktig-lru. Mer information finns i [Standardkonfiguration för Redis-server](cache-configure.md#default-redis-server-configuration). |
| Princip för förfallodatum |Standardprincipen för förfallodatum är Absolut och standardintervallet för förfallodatum är 10 minuter. Riktlinjer för glidande och aldrig är också tillgängliga. |Som standard förfaller inte objekt i cacheminnet, men en förfallodatum kan konfigureras per skrivbas med hjälp av cacheuppsättningsöverbelastningar. |
| Regioner och taggning |Regioner är undergrupper för cachelagrade objekt. Regioner stöder också anteckningen av cachelagrade objekt med ytterligare beskrivande strängar som kallas taggar. Regioner stöder möjligheten att utföra sökåtgärder på alla taggade objekt i den regionen. Alla objekt inom en region finns inom en enda nod i cacheklustret. |en Azure Cache för Redis består av en enda nod (såvida inte Redis-klustret är aktiverat) så att konceptet med regioner för hanterad cachetjänst inte gäller. Redis stöder sökning och jokertecken åtgärder när du hämtar nycklar så beskrivande taggar kan bäddas in i nyckelnamn och används för att hämta objekt senare. Ett exempel på att implementera en taggningslösning med Redis finns i [Implementera cachetaggning med Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serialisering |Managed Cache stöder NetDataContractSerializer, BinaryFormatter och användning av anpassade serialiserare. Standard är NetDataContractSerializer. |Det är klientprogrammets ansvar att serialisera .NET-objekt innan de placeras i cacheminnet, med valet av serialiseraren upp till klientprogramutvecklaren. Mer information och exempelkod finns [i Arbeta med .NET-objekt i cacheminnet](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Cache emulator |Hanterad cache ger en lokal cacheemulator. |Azure Cache för Redis har ingen emulator, men du kan [köra MSOpenTech-versionen av redis-server.exe lokalt](cache-faq.md#cache-emulator) för att ge en emulatorupplevelse. |

## <a name="choose-a-cache-offering"></a>Välj ett cacheerbjudande
Microsoft Azure Cache för Redis är tillgängligt på följande nivåer:

* **Grundläggande** – En nod. Flera storlekar upp till 53 GB.
* **Standard** – Primär/replik med två noder. Flera storlekar upp till 53 GB. 99,9 % SLA.
* **Premium** – Primär/replik med två noder och upp till 10 shards. Flera storlekar från 6 GB till 1,2 TB. Alla standardnivåfunktioner med mera, med stöd för [Redis-kluster](cache-how-to-premium-clustering.md), [Redis persistence](cache-how-to-premium-persistence.md) och [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9 % SLA.

Varje nivå har olika funktioner och priser. Funktionerna beskrivs senare i den här guiden och mer information om priser finns i [Information om cachepriser](https://azure.microsoft.com/pricing/details/cache/).

En utgångspunkt för migreringen är att välja den storlek som matchar storleken på den tidigare cachetjänsten för hanterad cache och sedan skala upp eller ned beroende på kraven i ditt program. Mer information om hur du väljer rätt Azure Cache för Redis-erbjudande finns i [Vad Azure Cache för Redis erbjuder och storlek ska jag använda](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Skapa en cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurera cacheklienterna
När cacheminnet har skapats och konfigurerats är nästa steg att ta bort konfigurationen för hanterad cachetjänst och lägga till Azure Cache for Redis-konfigurationen och referenser så att cacheklienter kan komma åt cachen.

* Ta bort konfigurationen för hanterade cachetjänster
* Konfigurera en cacheklient med StackExchange.Redis NuGet-paketet

### <a name="remove-the-managed-cache-service-configuration"></a>Ta bort konfigurationen för hanterade cachetjänster
Innan klientprogrammen kan konfigureras för Azure Cache for Redis måste de befintliga konfigurations- och sammansättningsreferenserna för managed cache service tas bort genom att avinstallera paketet Managed Cache Service NuGet.

Om du vill avinstallera paketet Managed Cache Service NuGet högerklickar du på klientprojektet i **Solution Explorer** och väljer **Hantera NuGet-paket**. Välj **noden Installerade paket** och skriv W**indowsAzure.Caching** i rutan Sök installerade paket. Välj **Windows** **Azure Cache** (eller **Windows** **Azure Caching** beroende på vilken version av NuGet-paketet), klicka på **Avinstallera**och klicka sedan på **Stäng**.

![Avinstallera Azure Managed Cache Service NuGet-paket](./media/cache-migrate-to-redis/IC757666.jpg)

Om du avinstallerar paketet Hanterad cachetjänst NuGet tas klientprogrammet bort de hanterade cachetjänstenheterna och posterna För hanterade cachetjänsten i klientprogrammets app.config eller web.config. Eftersom vissa anpassade inställningar kanske inte tas bort när du avinstallerar NuGet-paketet öppnar du web.config eller app.config och kontrollerar att följande element tas bort.

Kontrollera att `dataCacheClients` posten tas `configSections` bort från elementet. Ta inte bort `configSections` hela elementet. bara ta `dataCacheClients` bort posten, om den är närvarande.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Se till `dataCacheClients` att avsnittet tas bort. Avsnittet `dataCacheClients` kommer att likna följande exempel.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

När konfigurationen för managed cache service har tagits bort kan du konfigurera cacheklienten enligt beskrivningen i följande avsnitt.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurera en cacheklient med StackExchange.Redis NuGet-paketet
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrera tjänstkod för hanterad cache
API:et för StackExchange.Azure Cache för Redis-klienten liknar den hanterade cachetjänsten. Det här avsnittet innehåller en översikt över skillnaderna.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ansluta till cachen med klassen ConnectionMultiplexer
I Managed Cache Service hanterades anslutningar till `DataCacheFactory` `DataCache` cachen av klasserna och. I Azure Cache för Redis hanteras `ConnectionMultiplexer` dessa anslutningar av klassen.

Lägg till följande med hjälp av utdrag överst i alla filer som du vill komma åt cachen från.

```csharp
using StackExchange.Redis
```

Om det här namnområdet inte går att lösa måste du lägga till paketet StackExchange.Redis NuGet enligt beskrivningen i [Snabbstart: Använd Azure Cache för Redis med ett .NET-program](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Observera att StackExchange.Redis-klienten kräver .NET Framework 4 eller senare.
> 
> 

Om du vill ansluta till en Azure-cache för Redis-instans anropar du den statiska `ConnectionMultiplexer.Connect` metoden och skickar i slutpunkten och nyckeln. En metod för att dela en `ConnectionMultiplexer`-instans i ditt program är att ha en statisk egenskap som returnerar en ansluten instans, ungefär som i följande exempel. Den här metoden ger ett trådsäkert `ConnectionMultiplexer` sätt att initiera en enda ansluten instans. I det `abortConnect` här exemplet är inställt på false, vilket innebär att samtalet kommer att lyckas även om en anslutning till cachen inte har upprättats. En viktig egenskap i `ConnectionMultiplexer` är att anslutningen återställs automatiskt till cachen när nätverksproblemet eller andra fel är lösta.

```csharp
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
```

Cacheslutpunkten, nycklarna och portarna kan hämtas från **Azure Cache for Redis-bladet** för cacheinstansen. Mer information finns i [Azure Cache for Redis-egenskaper](cache-configure.md#properties).

När anslutningen har upprättats returnerar du en referens till Azure `ConnectionMultiplexer.GetDatabase` Cache for Redis-databasen genom att anropa metoden. Det objekt som returneras från `GetDatabase`-metoden är ett förenklat genomströmningsobjekt som inte behöver lagras.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

StackExchange.Redis-klienten `RedisKey` använder `RedisValue` och typerna för åtkomst till och lagring av objekt i cacheminnet. Dessa typer mappas till de flesta primitiva språktyper, inklusive sträng, och används ofta inte direkt. Redis-strängar är den mest grundläggande typen av Redis-värde och kan innehålla många typer av data, inklusive serialiserade binära `String` strömmar, och även om du inte får använda typen direkt använder du metoder som finns i namnet. För de flesta primitiva datatyper lagrar och hämtar `StringSet` `StringGet` du objekt från cacheminnet med metoderna och, såvida du inte lagrar samlingar eller andra Redis-datatyper i cacheminnet. 

`StringSet`och `StringGet` liknar den hanterade `Put` cachetjänsten och `Get` metoderna, med en stor skillnad är att innan du ställer in och får ett .NET-objekt i cacheminnet måste du serialisera den först. 

När `StringGet`du anropar , om objektet finns, returneras det och om det inte gör det returneras null. I det här fallet kan du hämta värdet från önskad datakälla och lagra det i cacheminnet för efterföljande användning. Det här mönstret kallas cache-aside-mönster.

Ange förfallodatum för ett objekt i cacheminnet med hjälp av `TimeSpan`-parametern för `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Cache för Redis kan fungera med .NET-objekt samt primitiva datatyper, men innan ett .NET-objekt kan cachelagras måste det serialiseras. Denna serialisering är programutvecklarens ansvar och ger utvecklaren flexibilitet i valet av serialiserare. Mer information och exempelkod finns [i Arbeta med .NET-objekt i cacheminnet](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrera ASP.NET sessionstillstånd och cachelagring av utdata till Azure Cache för Redis
Azure Cache för Redis har leverantörer för både ASP.NET sessionstillstånd och sidutdatacachelagring. Om du vill migrera ditt program som använder de hanterade cachetjänstversionerna av dessa leverantörer tar du först bort de befintliga avsnitten från din web.config och konfigurerar sedan Azure Cache for Redis-versionerna av leverantörerna. Instruktioner om hur du använder Azure Cache för Redis-ASP.NET-leverantörer finns [i ASP.NET Sessionstillståndsprovider för Azure Cache för Redis](cache-aspnet-session-state-provider.md) och [ASP.NET Utdatacacheprovider för Azure Cache för Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Nästa steg
Utforska [Azure Cache for Redis-dokumentationen](https://azure.microsoft.com/documentation/services/cache/) för självstudier, exempel, videor med mera.

