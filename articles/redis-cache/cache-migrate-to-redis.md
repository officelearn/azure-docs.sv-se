---
title: Migrera program för Managed Cache Service till Redis - Azure | Microsoft Docs
description: Lär dig hur du migrerar Managed Cache Service och cachelagring i Rollinstanser program till Azure Redis Cache
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/30/2017
ms.author: wesmc
ms.openlocfilehash: f499925ecea8ca127c90691f7d92e74e8df68cf9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697347"
---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Migrera från Managed Cache Service till Azure Redis Cache
Migrera dina program som använder Azure Managed Cache Service till Azure Redis Cache kan åstadkommas med minimala ändringar till ditt program, beroende på vilka Managed Cache Service-funktioner som programmets cachelagring. API: er är inte exakt samma de liknar varandra och mycket av din befintliga kod som använder Managed Cache Service för att få åtkomst till en cache kan återanvändas med minimala ändringar. Den här artikeln visar hur du gör den nödvändiga konfigurationen och programändringar att migrera dina Managed Cache Service-appar använder Azure Redis Cache och visar hur några av funktionerna i Azure Redis Cache kan användas för att implementera funktionerna i en Managed Cache Service cache.

>[!NOTE]
>Managed Cache Service och cachelagring i Rollinstanser har [dras tillbaka](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) den 30 November 2016. Om du har alla distributioner för Cache i roller som du vill migrera till Azure Redis Cache kan följa du stegen i den här artikeln.

## <a name="migration-steps"></a>Migreringssteg
Följande steg krävs för att migrera en Managed Cache Service-program som använder Azure Redis Cache.

* Mappa Managed Cache Service-funktioner till Azure Redis Cache
* Välj en Cache-erbjudande
* Skapa ett cacheminne
* Konfigurera Cacheklienterna
  * Ta bort konfiguration för Managed Cache Service
  * Konfigurera en cacheklient med StackExchange.Redis NuGet-paket
* Migrera kod för Managed Cache Service
  * Ansluta till cachen med klassen ConnectionMultiplexer
  * Åtkomst primitiva datatyper i cacheminnet
  * Arbeta med .NET-objekt i cachen
* Migrera ASP.NET-sessionstillstånd och cachelagring av utdata till Azure Redis Cache 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Mappa Managed Cache Service-funktioner till Azure Redis Cache
Azure Managed Cache Service och Azure Redis Cache är liknande, men implementerar en del av deras funktioner på olika sätt. Det här avsnittet beskriver några av skillnaderna och ger vägledning om hur du implementerar funktionerna i Managed Cache Service i Azure Redis Cache.

| Managed Cache Service-funktion | Managed Cache Service-stöd | Support för Azure Redis Cache |
| --- | --- | --- |
| Namngivna cacheminnen |En standard-cache är konfigurerad och i Standard och Premium-cache-erbjudanden, upp till nio ytterligare med namnet cacheminnen kan konfigureras om så önskas. |Azure Redis-cacheminnen har ett konfigurerbart antal databaser (16 är standard) som kan användas för att implementera en liknande funktion till namngivna cacheminnen. Mer information finns i [What are Redis databases?](cache-faq.md#what-are-redis-databases) (Vad är Redis-databaser?) och [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Standardkonfiguration av Redis-server). |
| Hög tillgänglighet |Ger hög tillgänglighet för objekt i cachen i cache-erbjudanden för Standard och Premium. Om objekt går förlorade på grund av ett fel kan är säkerhetskopior av objekt i cacheminnet fortfarande tillgängliga. Skrivningar till den sekundära cachen görs synkront. |Hög tillgänglighet är tillgängligt i Standard och Premium-cache-erbjudanden, som har en konfiguration för primär/replik av två noder (varje shard i en Premium-cache har ett par med primär/replik). Skrivningar till repliken görs asynkront. Mer information finns i [priser för Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/). |
| Meddelanden |Gör att klienter kan ta emot asynkrona meddelanden när ett urval cacheåtgärder görs i en namngiven cache. |Klientprogram kan använda Redis pub/sub eller [Keyspace-meddelanden](cache-configure.md#keyspace-notifications-advanced-settings) att uppnå en liknande funktion att meddelanden. |
| Lokal cache |Lagrar en kopia av cachelagrade objekt som lokalt på klienten för extra snabb åtkomst. |Klientprogram behöver du implementera den här funktionen med hjälp av en ordlista eller liknande datastruktur. |
| Princip för borttagning |Ingen eller LRU. Standardprincipen är LRU. |Azure Redis Cache har stöd för följande avlägsningsprinciper: beräkningsbara lru, allkeys lru, beräkningsbara slumpmässiga, allkeys slumpmässiga beräkningsbara-ttl, noeviction. Standardprincipen är beräkningsbara lru. Mer information finns i [Default Redis server configuration](cache-configure.md#default-redis-server-configuration). |
| Princip |Standardprincipen för förfallodatum är absoluta och upphör att gälla Standardintervallet är 10 minuter. Glidande och aldrig principer är också tillgängliga. |Som standard upphör inte objekt i cacheminnet, men ett förfallodatum kan konfigureras på basis av per skrivning med hjälp av cache set överlagringar. |
| Regioner och taggning |Regionerna är undergrupper för cachelagrade objekt. Regioner stöder också anteckningen av cachelagrade objekt med ytterligare beskrivande strängar som kallas taggar. Regioner har stöd för möjligheten att utföra sökningar på alla objekt som taggats i den regionen. Alla objekt inom en region finns i en enda nod i cacheklustret. |En Redis-cache består av en enskild nod (såvida inte Redis-kluster är aktiverat) så inte gäller begreppet Managed Cache Service regioner. Redis stöder sökning och jokertecken åtgärder vid hämtning av nycklar så beskrivande taggar kan vara inbäddad i nyckelnamnen och används för att hämta objekten senare. Ett exempel för att implementera en taggning lösning med hjälp av Redis finns i [implementera taggning med Redis cache](http://stackify.com/implementing-cache-tagging-redis/). |
| Serialisering |Managed Cache stöder NetDataContractSerializer BinaryFormatter och användningen av anpassade serializers. Standardvärdet är NetDataContractSerializer. |Det är ansvar för klientprogram att serialisera .NET-objekt innan du placerar dem i cachen med valet av serialiserare upp till programutvecklare för klienten. Mer information och exempelkod finns i [arbeta med .NET-objekt i cachen](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Cache-emulatorn |Hanterad Cache ger en lokal cache-emulator. |Azure Redis Cache har inte en emulator, men du kan [kör MSOpenTech version av redis-server.exe lokalt](cache-faq.md#cache-emulator) ska få en emulator-upplevelse. |

## <a name="choose-a-cache-offering"></a>Välj en Cache-erbjudande
Microsoft Azure Redis Cache finns tillgängligt på följande nivåer:

* **Grundläggande** – En nod. Flera storlekar upp till 53 GB.
* **Standard** – Primär/replik med två noder. Flera storlekar upp till 53 GB. 99,9 % SLA.
* **Premium** – Primär/replik med två noder och upp till 10 shards. Flera storlekar från 6 GB till 530 GB. Alla standardnivåfunktioner med mera, med stöd för [Redis-kluster](cache-how-to-premium-clustering.md), [Redis persistence](cache-how-to-premium-persistence.md) och [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9 % SLA.

Varje nivå har olika funktioner och priser. Funktionerna beskrivs senare i den här guiden och mer information om priser, se [prisinformationen för Cache](https://azure.microsoft.com/pricing/details/cache/).

En startpunkt för migrering är att välja den storlek som matchar storleken på cacheminnet tidigare Managed Cache Service och sedan skala upp eller ned beroende på kraven för ditt program. Läs mer om att välja rätt Azure Redis Cache-erbjudande, [vilket Redis Cache-erbjudande och vilken storlek ska jag använda](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Skapa ett cacheminne
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurera Cacheklienterna
När cachen har skapats och konfigurerats, nästa steg är att ta bort konfiguration för Managed Cache Service och lägga till Azure Redis Cache-konfiguration och refererar till så att cache-klienter kan komma åt cachen.

* Ta bort konfiguration för Managed Cache Service
* Konfigurera en cacheklient med StackExchange.Redis NuGet-paket

### <a name="remove-the-managed-cache-service-configuration"></a>Ta bort konfiguration för Managed Cache Service
Innan klientprogrammen kan konfigureras för Azure Redis Cache, den befintliga konfigurationen för Managed Cache Service och sammansättningsreferenser måste tas bort genom att avinstallera Managed Cache Service NuGet-paketet.

Om du vill avinstallera Managed Cache Service NuGet-paketet, högerklicka på klientprojektet i **Solution Explorer** och välj **hantera NuGet-paket**. Välj den **installerade paket** noden och Skriv W**indowsAzure.Caching** i rutan Sök installerade paket. Välj **Windows** **Azure Cache** (eller **Windows** **Azure Caching** beroende på vilken version av NuGet-paketet), klickar du på **Avinstallera**, och klicka sedan på **Stäng**.

![Avinstallera Azure Managed Cache Service NuGet-paketet](./media/cache-migrate-to-redis/IC757666.jpg)

Avinstallerar Managed Cache Service NuGet-paketet tas bort sammansättningarna Managed Cache Service och Managed Cache Service-poster i app.config eller web.config i klientprogrammet. Eftersom vissa anpassade inställningar inte kan tas bort när du avinstallerar NuGet-paketet, öppna web.config eller app.config och kontrollera att följande element har tagits bort.

Se till att den `dataCacheClients` tas bort från den `configSections` element. Ta inte bort hela `configSections` elementet; bara ta bort den `dataCacheClients` transaktionen, om den finns.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Se till att den `dataCacheClients` avsnittet tas bort. Den `dataCacheClients` avsnitt ska vara detsamma som i följande exempel.

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

När Managed Cache Service-konfiguration har tagits bort, kan du konfigurera cacheklienten enligt beskrivningen i följande avsnitt.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurera en cacheklient med StackExchange.Redis NuGet-paket
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrera kod för Managed Cache Service
API för cacheklienten stackexchange.redis liknar Managed Cache Service. Det här avsnittet innehåller en översikt över skillnaderna.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ansluta till cachen med klassen ConnectionMultiplexer
I Managed Cache Service, anslutningar till cachen hanterades av den `DataCacheFactory` och `DataCache` klasser. I Azure Redis Cache, dessa anslutningar hanteras av den `ConnectionMultiplexer` klass.

Lägg till följande användningsinstruktion högst upp i den fil som du vill komma åt cachen.

```csharp
using StackExchange.Redis
```

Om det inte går att lösa det här namnområdet är det viktigt att du har lagt till StackExchange.Redis NuGet-paketet enligt beskrivningen i [Snabbstart: använder Azure Redis Cache med ett .NET-program](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Observera att StackExchange.Redis-klienten kräver .NET Framework 4 eller senare.
> 
> 

Om du vill ansluta till en Azure Redis Cache-instans, anropar du statiskhet `ConnectionMultiplexer.Connect` metoden samt skickar slutpunkten och nyckeln. En metod för att dela en `ConnectionMultiplexer`-instans i ditt program är att ha en statisk egenskap som returnerar en ansluten instans, ungefär som i följande exempel. Den här metoden ger ett trådsäkert sätt för att initiera en enskild ansluten `ConnectionMultiplexer` instans. I det här exemplet `abortConnect` är inställd på false, vilket innebär att anropet lyckas även om det inte är att upprätta en anslutning till cachen. En viktig egenskap i `ConnectionMultiplexer` är att anslutningen återställs automatiskt till cachen när nätverksproblemet eller andra fel är lösta.

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

Det cache-slutpunkten, nycklar och portar kan hämtas från den **Redis Cache** bladet för din cache-instans. Mer information finns i [Redis Cache-egenskaper](cache-configure.md#properties).

När anslutningen har upprättats kan returnera en referens till Redis-cachedatabasen genom att anropa den `ConnectionMultiplexer.GetDatabase` metoden. Det objekt som returneras från `GetDatabase`-metoden är ett förenklat genomströmningsobjekt som inte behöver lagras.

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

StackExchange.Redis-klienten använder den `RedisKey` och `RedisValue` typer för att komma åt och lagra objekt i cacheminnet. Dessa typer mappa till mest primitiva språktyper, inklusive sträng, och ofta används inte direkt. Redis-strängar är den enklaste typen av Redis-värde och kan innehålla flera typer av data, inklusive serialiserade binära dataströmmar, och du inte får använda typen direkt, men du kommer använda metoder med `String` i namnet. För de flesta primitiva datatyper du lagra och hämta objekt från cachen med den `StringSet` och `StringGet` metoder, såvida inte du lagrar samlingar eller andra Redis-datatyper i cacheminnet. 

`StringSet` och `StringGet` liknar Managed Cache Service `Put` och `Get` metoder, med en större skillnaden är att innan du kan ange och hämta ett .NET-objekt till cachen du måste serialisera först. 

När du anropar `StringGet`om objektet finns returneras och om det inte null returneras. I det här fallet kan du hämta värdet från önskad datakälla och lagra det i cacheminnet för senare användning. Det här mönstret kallas för cache-aside-mönstret.

Ange förfallodatum för ett objekt i cacheminnet med hjälp av `TimeSpan`-parametern för `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Redis Cache kan arbeta med .NET-objekt och basdatatyper, men måste serialiseras innan ett .NET-objekt kan cachelagras. Den här serialiseringen är programutvecklarens ansvar och ger utvecklaren flexibilitet i valet av serialiserare. Mer information och exempelkod finns i [arbeta med .NET-objekt i cachen](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Migrera ASP.NET-sessionstillstånd och cachelagring av utdata till Azure Redis Cache
Azure Redis Cache har providers för både ASP.NET-sessionstillstånd och cachelagring av utdata för sidan. Om du vill migrera ditt program som använder Managed Cache Service-versionerna av dessa providers först ta bort de befintliga avsnitt från filen web.config och konfigurera Azure Redis Cache-versioner av providers. Anvisningar om hur du använder Azure Redis Cache ASP.NET-providers finns i [ASP.NET-Sessionstillståndsprovider för Azure Redis Cache](cache-aspnet-session-state-provider.md) och [ASP.NET-Utdatacacheprovider för Azure Redis Cache](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Nästa steg
Utforska den [dokumentation för Azure Redis Cache](https://azure.microsoft.com/documentation/services/cache/) för självstudier, exempel, videor och mycket mer.

