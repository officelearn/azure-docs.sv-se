---
title: Migrera program Managed Cache Service till Redis - Azure | Microsoft Docs
description: "Lär dig hur du migrerar Managed Cache Service och cachelagring i Rollinstanser program till Azure Redis-Cache"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/30/2017
ms.author: sdanie
ms.openlocfilehash: 0fbfb945c66926794721f2ce8cc183dac51ecb27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrera från Managed Cache Service till Azure Redis-Cache
Migrera dina program som använder Azure Managed Cache Service till Azure Redis-Cache kan åstadkommas med minimala ändringar till ditt program, beroende på Managed Cache Service-funktioner som används av tillämpningsprogrammet cachelagring. När de API: er är inte exakt samma de liknar och en stor del av din befintliga kod som använder Managed Cache Service för att komma åt en cache kan återanvändas med minimala ändringar. Det här avsnittet visar hur du gör nödvändiga konfigurationen och programändringar migrera dina Managed Cache Service-program att använda Azure Redis-Cache och visar hur några av funktionerna i Azure Redis-Cache kan användas för att implementera funktionerna i en Managed Cache Service-cache.

>[!NOTE]
>Hanterade Cache-tjänst och cachelagring i Rollinstanser har [dragits tillbaka](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 November 2016. Om du har några distributioner för cachelagring i Rollinstanser som du vill migrera till Azure Redis-Cache kan följa du stegen i den här artikeln.

## <a name="migration-steps"></a>Migreringssteg
Följande steg krävs för att migrera en Managed Cache Service-program att använda Azure Redis-Cache.

* Mappa Managed Cache Service funktioner till Azure Redis-Cache
* Välj ett erbjudande för Cache
* Skapa ett cacheminne
* Konfigurera Cache-klienter
  * Ta bort konfigurationen för Managed Cache Service
  * Konfigurera en cacheklient som använder StackExchange.Redis NuGet-paketet
* Migrera koden Managed Cache Service
  * Ansluta till cache med hjälp av klassen ConnectionMultiplexer
  * Åtkomst primitiva datatyper i cacheminnet
  * Arbeta med .NET-objekt i cachen
* Migrera sessionstillståndet ASP.NET och cachelagring av utdata till Azure Redis-Cache 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Mappa Managed Cache Service funktioner till Azure Redis-Cache
Azure Managed Cache Service och Azure Redis-Cache är samma men implementerar en del av deras funktioner på olika sätt. Det här avsnittet beskrivs några av skillnaderna och ger vägledning om hur du implementerar funktionerna i Managed Cache Service i Azure Redis-Cache.

| Funktionen för hanterade Cache Service | Stöd för hanterade Cache Service | Stöd för Azure Redis-Cache |
| --- | --- | --- |
| Namngivna cacheminnen |En standard-cache är konfigurerad och i Standard- och Premium-cachen erbjudanden, upp till nio ytterligare med namnet cacheminnen kan konfigureras om så önskas. |Azure Redis-cache har en konfigurerbar antalet databaser (standard 16) som kan användas för att implementera en liknande funktionalitet till namngivna cacheminnen. Mer information finns i [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Vad är Redis-databaser?) och [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Standardkonfiguration av Redis-server). |
| Hög tillgänglighet |Ger hög tillgänglighet för objekt i cachen i cache-erbjudanden Standard och Premium. Om objekt går förlorade på grund av ett fel kan är säkerhetskopior av objekten i cachen fortfarande tillgängliga. Skrivningar till sekundär cache görs synkront. |Hög tillgänglighet finns i Standard- och Premium cache erbjudanden, som har en konfiguration för primär/replik av två noder (varje Fragmentera i Premium-cache har två primära/replik). Skrivningar till repliken görs asynkront. Mer information finns i [priser för Azure Redis-Cache](https://azure.microsoft.com/pricing/details/cache/). |
| Meddelanden |Tillåter att klienter på asynkrona meddelanden när en mängd olika cache-åtgärder sker på en namngiven cache. |Klientprogram kan använda Redis pub/sub eller [Keyspace-meddelanden](cache-configure.md#keyspace-notifications-advanced-settings) för att få en liknande funktion för meddelanden. |
| Lokal cache |Lagrar en kopia av cachelagrade objekt lokalt på klienten för extra snabb åtkomst. |Klientprogram måste du implementera den här funktionen med hjälp av en ordlista eller liknande datastruktur. |
| Princip för borttagning |Ingen eller LRU. Standardprincipen är LRU. |Azure Redis-Cache har stöd för följande principer för borttagning: obeständigt lru, allkeys lru, obeständigt slumpmässiga, allkeys slumpmässiga obeständigt-TTL-värde, noeviction. Standardprincipen är obeständigt lru. Mer information finns i [standard Redis serverkonfiguration](cache-configure.md#default-redis-server-configuration). |
| Princip |Standardprincipen för förfallodatum är absolut förfallodatum Standardintervallet är tio minuter. Det finns också glidande och aldrig principer. |Som standard förfaller inte objekt i cacheminnet, men ett förfallodatum kan konfigureras på grundval av per skrivning med hjälp av cachen set överlagringar. Mer information finns i [Lägg till och hämta objekt från cache](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache). |
| Regioner och taggning |Regioner är undergrupper för cachelagrade objekt. Regioner stöder också anteckningen cachelagrade objekt med ytterligare beskrivande strängar som kallas taggar. Regioner stöder möjligheten att utföra sökningar på taggade objekt i den regionen. Alla objekt i en region finns inom en enskild nod i klustret cache. |Ett Redis-cache består av en enskild nod (om Redis cluster är aktiverad) så inte gäller begreppet Managed Cache Service regioner. Redis har stöd för sökning och jokertecken åtgärder vid hämtning av nycklar så beskrivande taggar kan är inbäddad i nyckelnamnen och används för att hämta objekten senare. Ett exempel på att implementera en märkning lösning med Redis finns [implementera märkning med Redis cache](http://stackify.com/implementing-cache-tagging-redis/). |
| Serialisering |Managed Cache stöder NetDataContractSerializer BinaryFormatter och användningen av anpassade serializers. Standardvärdet är NetDataContractSerializer. |Det är ansvar av klientprogram att serialisera .NET objekt innan du monterar dem till cachen med val av serialiseraren upp till klienten programutvecklaren. Mer information och exempelkod finns [arbeta med .NET-objekt i cacheminnet](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Cache-emulatorn |Hanterade cachen innehåller en lokal cache-emulatorn. |Azure Redis-Cache har inte en emulator, men du kan [kör MSOpenTech version av redis-server.exe lokalt](cache-faq.md#cache-emulator) att tillhandahålla en emulator. |

## <a name="choose-a-cache-offering"></a>Välj ett erbjudande för Cache
Microsoft Azure Redis Cache finns tillgängligt på följande nivåer:

* **Grundläggande** – En nod. Flera storlekar upp till 53 GB.
* **Standard** – Primär/replik med två noder. Flera storlekar upp till 53 GB. 99,9 % SLA.
* **Premium** – Primär/replik med två noder och upp till 10 shards. Flera storlekar från 6 GB till 530 GB. Alla standardnivåfunktioner med mera, med stöd för [Redis-kluster](cache-how-to-premium-clustering.md), [Redis persistence](cache-how-to-premium-persistence.md) och [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9 % SLA.

Varje nivå har olika funktioner och priser. Funktionerna beskrivs senare i den här guiden och mer information om priser finns i avsnittet [Cache-prisinformation](https://azure.microsoft.com/pricing/details/cache/).

En startpunkt för migrering är att välja den storlek som matchar storleken på din tidigare Managed Cache Service-cache och sedan skala upp eller ned beroende på kraven i ditt program. Mer information om att välja rätt Azure Redis-Cache-erbjudande finns [vilka Redis-Cache erbjudande och vilken storlek ska jag använda?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Skapa ett cacheminne
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurera Cache-klienter
När cachen har skapats och konfigurerats, nästa steg är att ta bort konfigurationen för Managed Cache Service och Lägg till Lägg till Azure Redis-Cache-konfigurationen och refererar till så att cacheklienter kan komma åt cachen.

* Ta bort konfigurationen för Managed Cache Service
* Konfigurera en cacheklient som använder StackExchange.Redis NuGet-paketet

### <a name="remove-the-managed-cache-service-configuration"></a>Ta bort konfigurationen för Managed Cache Service
Innan klientprogrammen kan konfigureras för Azure Redis-Cache, den befintliga Managed Cache Service-konfigurationen och sammansättningsreferenser måste tas bort genom att avinstallera Managed Cache Service NuGet-paketet.

Om du vill avinstallera Managed Cache Service NuGet-paketet, högerklicka på klientprojektet i **Solution Explorer** och välj **hantera NuGet-paket**. Välj den **installerade paket** nod och typen W**indowsAzure.Caching** i sökrutan för installerade paket. Välj **Windows** **Azure Cache** (eller **Windows** **Azure Caching** beroende på vilken version av NuGet-paketet), klickar du på **avinstallera**, och klicka sedan på **Stäng**.

![Avinstallera NuGet-paketet för Azure Managed Cache Service](./media/cache-migrate-to-redis/IC757666.jpg)

Avinstallerar Managed Cache Service NuGet-paketet tas bort Managed Cache Service-sammansättningar och Managed Cache Service-poster i app.config eller web.config av klientprogram. Eftersom vissa anpassade inställningar tas inte bort när du avinstallerar NuGet-paketet, öppna web.config eller app.config och kontrollera att följande element har tagits bort.

Se till att den `dataCacheClients` tas bort från den `configSections` element. Ta inte bort hela `configSections` elementet; bara ta bort den `dataCacheClients` posten, om den finns.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Se till att den `dataCacheClients` avsnittet tas bort. Den `dataCacheClients` avsnittet kommer att likna följande exempel.

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

När Managed Cache Service-konfigurationen har tagits bort, kan du konfigurera cacheklienten som beskrivs i följande avsnitt.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurera en cacheklient som använder StackExchange.Redis NuGet-paketet
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrera koden Managed Cache Service
API för cacheklienten StackExchange.Redis liknar Managed Cache Service. Det här avsnittet innehåller en översikt över skillnaderna.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ansluta till cache med hjälp av klassen ConnectionMultiplexer
I Managed Cache Service anslutningar till cachen hanterades av den `DataCacheFactory` och `DataCache` klasser. I Azure Redis-Cache dessa anslutningar som hanteras av den `ConnectionMultiplexer` klass.

Lägg till följande med instruktionen till början av en fil som du vill komma åt cachen.

```c#
using StackExchange.Redis
```

Om inte går att lösa det här namnområdet är det viktigt att du har lagt till StackExchange.Redis NuGet-paketet enligt beskrivningen i [konfigurera cache-klienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

> [!NOTE]
> Observera att StackExchange.Redis klienten kräver .NET Framework 4 eller senare.
> 
> 

För att ansluta till en Azure Redis-Cache-instans, anropar statiskhet `ConnectionMultiplexer.Connect` metoden och skicka slutpunkt och nyckel. En metod för att dela en `ConnectionMultiplexer`-instans i ditt program är att ha en statisk egenskap som returnerar en ansluten instans, ungefär som i följande exempel. Detta ger ett trådsäkert sätt att endast initiera en enda ansluten `ConnectionMultiplexer`-instans. I det här exemplet `abortConnect` är inställd på false, vilket innebär att anropet lyckas även om inte är det att upprätta en anslutning till cachen. En viktig egenskap i `ConnectionMultiplexer` är att anslutningen återställs automatiskt till cachen när nätverksproblemet eller andra fel är lösta.

```c#
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

Cache-slutpunkten, nycklar och portar kan hämtas från den **Redis-Cache** bladet för din cache-instans. Mer information finns i [Redis-Cache egenskaper](cache-configure.md#properties).

När anslutningen har upprättats kan returnera en referens till Redis-cache-databasen genom att anropa den `ConnectionMultiplexer.GetDatabase` metoden. Det objekt som returneras från `GetDatabase`-metoden är ett förenklat genomströmningsobjekt som inte behöver lagras.

```c#
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

StackExchange.Redis klienten använder den `RedisKey` och `RedisValue` typer för att komma åt och lagra objekt i cacheminnet. Dessa typer av mappar till mest primitiva språktyper, inklusive sträng, och ofta används inte direkt. Redis-strängar är den enklaste typen av Redis värde och kan innehålla flera typer av data, inklusive serialiserade binära dataströmmar och du kan inte använda typen direkt, du använder metoder som innehåller `String` i namnet. För de flesta primitiva datatyper du lagra och hämta objekt från en cache med hjälp av den `StringSet` och `StringGet` metoder, om du lagrar samlingar eller andra Redis-datatyper i cacheminnet. 

`StringSet`och `StringGet` liknar Managed Cache Service `Put` och `Get` metoder med en större skillnad är att innan du ställer in och få en .NET-objekt i cacheminnet du måste serialisera först. 

När du anropar `StringGet`om objektet finns, returneras och om det inte är null returneras. I det här fallet kan du hämta värdet från den önskade datakällan och lagra det i cacheminnet för senare användning. Detta kallas för ett cache aside-mönster.

Ange förfallodatum för ett objekt i cacheminnet med hjälp av `TimeSpan`-parametern för `StringSet`.

```c#
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Redis-Cache kan arbeta med .NET-objekt som primitiva datatyper, men måste serialiseras innan ett .NET-objekt som kan cachelagras. Detta är programutvecklaren ansvar. Detta ger utvecklare möjlighet vid val av serialiseraren. Mer information och exempelkod finns [arbeta med .NET-objekt i cacheminnet](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrera sessionstillståndet ASP.NET och cachelagring av utdata till Azure Redis-Cache
Azure Redis-Cache har providers för både sessionstillståndet ASP.NET och cachelagringen av sidan. Om du vill migrera dina program som använder Managed Cache Service-versioner av dessa providers först ta bort de befintliga avsnitt från filen web.config och konfigurera sedan Azure Redis-Cache-versioner av providrarna. Anvisningar om hur du använder Azure Redis-Cache ASP.NET-providers finns [ASP.NET-Sessionstillståndsprovider för Azure Redis-Cache](cache-aspnet-session-state-provider.md) och [ASP.NET Utdatacacheprovider för Azure Redis-Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Nästa steg
Utforska den [dokumentation för Azure Redis-Cache](https://azure.microsoft.com/documentation/services/cache/) för självstudier, exempel, videor och mycket mer.

