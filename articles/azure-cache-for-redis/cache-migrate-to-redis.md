---
title: Migrera Managed Cache Service program till Redis – Azure
description: Lär dig hur du migrerar Managed Cache Service och Cachelagring i rollinstanser program till Azure cache för Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: yegu
ROBOTS: NOINDEX
ms.openlocfilehash: 4e867f28209230cf33b0f94e7cc8ca12d015ff15
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008567"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis-deprecated"></a>Migrera från Managed Cache Service till Azure cache för Redis (inaktuell)
Migrering av program som använder Azure Managed Cache Service till Azure cache för Redis kan utföras med minimala ändringar i programmet, beroende på vilka Managed Cache Service funktioner som används i ditt caching-program. Även om API: erna inte är exakt samma som de är lika, och mycket av din befintliga kod som använder Managed Cache Service för att komma åt en cache kan återanvändas med minimala ändringar. Den här artikeln visar hur du gör nödvändiga konfigurations-och program ändringar för att migrera dina Managed Cache Service-program till att använda Azure cache för Redis och visar hur vissa av funktionerna i Azure cache för Redis kan användas för att implementera funktionerna i en Managed Cache Service cache.

>[!NOTE]
>Managed Cache Service och Cachelagring i rollinstanser drogs [tillbaka](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 november 2016. Om du har Cachelagring i rollinstanser distributioner som du vill migrera till Azure cache för Redis kan du följa stegen i den här artikeln.

## <a name="migration-steps"></a>Migrerings steg
Följande steg krävs för att migrera ett Managed Cache Service-program för att använda Azure cache för Redis.

* Mappa Managed Cache Service funktioner till Azure cache för Redis
* Välj ett cache-erbjudande
* Skapa en cache
* Konfigurera cache-klienterna
  * Ta bort Managed Cache Service-konfigurationen
  * Konfigurera en cache-klient med hjälp av StackExchange. Redis NuGet-paketet
* Migrera Managed Cache Services kod
  * Ansluta till cachen med klassen ConnectionMultiplexer
  * Åtkomst till primitiva data typer i cacheminnet
  * Arbeta med .NET-objekt i cachen
* Migrera ASP.NET-sessionstillstånd och cachelagring av utdata till Azure cache för Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mappa Managed Cache Service funktioner till Azure cache för Redis
Azure Managed Cache Service och Azure cache för Redis är likartade men implementerar några av funktionerna på olika sätt. Det här avsnittet beskriver några av skillnaderna och ger vägledning om hur du implementerar funktionerna i Managed Cache Service i Azure cache för Redis.

| Managed Cache Service funktion | Managed Cache Service support | Azure cache för Redis-support |
| --- | --- | --- |
| Namngivna cacheminnen |En standardcache har kon figurer ATS och i erbjudandena för standard-och Premium-cache kan upp till nio ytterligare namngivna cacheminnen konfigureras om det behövs. |Azure cache för Redis har ett konfigurerbart antal databaser (standard 16) som kan användas för att implementera en liknande funktion i namngivna cacheminnen. Mer information finns i [What are Redis databases?](cache-development-faq.md#what-are-redis-databases) (Vad är Redis-databaser?) och [Default Redis server configuration](cache-configure.md#default-redis-server-configuration) (Standardkonfiguration av Redis-server). |
| Hög tillgänglighet |Ger hög tillgänglighet för objekt i cachen i standard-och Premium cache-erbjudandena. Om objekten förloras på grund av ett problem är säkerhets kopior av objekten i cachen fortfarande tillgängliga. Skrivningar till replik-cachen görs synkront. |Hög tillgänglighet är tillgängligt i cacheminnet för standard-och Premium-cache, som har en primär-/replik konfiguration med två noder (varje Shard i en Premium-cache har ett primärt/replik par). Skrivningar till repliken görs asynkront. Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/). |
| Meddelanden |Tillåter att klienter tar emot asynkrona meddelanden när en rad olika cache-åtgärder sker i ett namngivet cacheminne. |Klient program kan använda Redis i pub/sub eller nyckel [utrymme](cache-configure.md#keyspace-notifications-advanced-settings) för att få liknande funktioner som aviseringar. |
| Lokal cache |Lagrar en kopia av cachelagrade objekt lokalt på klienten för extra snabb åtkomst. |Klient program måste implementera den här funktionen med hjälp av en ord lista eller liknande data struktur. |
| Borttagnings princip |Ingen eller LRU. Standard principen är LRU. |Azure cache för Redis har stöd för följande avlägsna principer: volatile-LRU, allkeys-LRU, volatile-slumpmässig, allkeys-Random, flyktig-TTL, noavlägsning. Standard principen är volatile-LRU. Mer information finns i [standard konfigurationen för Redis-servern](cache-configure.md#default-redis-server-configuration). |
| Princip för förfallo datum |Standard principen för förfallo datum är absolut och standard intervallet för förfallo tid är 10 minuter. Principer för glidande och aldrig är också tillgängliga. |Som standard upphör objekt i cachen inte att gälla, men ett förfallo datum kan konfigureras per skrivning med hjälp av cache set Overloads. |
| Regioner och taggning |Regioner är under grupper för cachelagrade objekt. Regioner stöder även anteckningen av cachelagrade objekt med ytterligare beskrivande strängar som kallas taggar. Regioner stöder möjligheten att utföra Sök åtgärder på alla taggade objekt i den regionen. Alla objekt inom en region finns i en enda nod i cache klustret. |en Azure-cache för Redis består av en enda nod (om inte Redis-kluster är aktiverat) så att begreppet Managed Cache Service regioner inte gäller. Redis stöder sökning och jokertecken vid hämtning av nycklar så att beskrivande taggar kan bäddas in i nyckel namnen och användas för att hämta objekten senare. Ett exempel på hur du implementerar en taggad lösning med hjälp av Redis finns i [implementera cache-taggning med Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serialisering |Hanterad cache stöder NetDataContractSerializer, BinaryFormatter och användning av anpassade serialiserare. Standardvärdet är NetDataContractSerializer. |Det är klient Programets ansvar att serialisera .NET-objekt innan de placeras i cachen, med valet av serialiserare till klient program utvecklaren. Mer information och exempel kod finns i [arbeta med .net-objekt i cacheminnet](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Cache-emulator |Hanterad cache tillhandahåller en lokal cache-emulator. |Azure cache för Redis har ingen emulator, men du kan [köra Redis lokalt](cache-development-faq.md#is-there-a-local-emulator-for-azure-cache-for-redis) för att tillhandahålla en emulator. |

## <a name="choose-a-cache-offering"></a>Välj ett cache-erbjudande
Microsoft Azure cache för Redis är tillgängligt på följande nivåer:

* **Grundläggande** – En nod. Flera storlekar upp till 53 GB.
* **Standard** – Primär/replik med två noder. Flera storlekar upp till 53 GB. 99,9 % SLA.
* **Premium** – Primär/replik med två noder och upp till 10 shards. Flera storlekar från 6 GB till 1,2 TB. Alla standardnivåfunktioner med mera, med stöd för [Redis-kluster](cache-how-to-premium-clustering.md), [Redis persistence](cache-how-to-premium-persistence.md) och [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9 % SLA.

Varje nivå har olika funktioner och priser. Funktionerna beskrivs senare i den här hand boken och mer information om prissättning finns i informationen om [cache-prissättning](https://azure.microsoft.com/pricing/details/cache/).

En start punkt för migrering är att välja den storlek som stämmer överens med storleken på din tidigare Managed Cache Service cache och sedan skala upp eller ned beroende på programmets krav. Mer information om hur du väljer rätt Azure-cache för Redis-erbjudande finns i [välja rätt nivå](cache-overview.md#choosing-the-right-tier).

## <a name="create-a-cache"></a>Skapa en cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Konfigurera cache-klienterna
När cachen har skapats och kon figurer ATS är nästa steg att ta bort Managed Cache Service-konfigurationen och lägga till Azure-cachen för Redis-konfiguration och referenser så att cache-klienter kan komma åt cachen.

* Ta bort Managed Cache Service-konfigurationen
* Konfigurera en cache-klient med hjälp av StackExchange. Redis NuGet-paketet

### <a name="remove-the-managed-cache-service-configuration"></a>Ta bort Managed Cache Service-konfigurationen
Innan klient programmen kan konfigureras för Azure cache för Redis, måste befintliga Managed Cache Service konfigurations-och sammansättnings referenser tas bort genom att avinstallera Managed Cache Service NuGet-paketet.

Om du vill avinstallera Managed Cache Service NuGet-paketet högerklickar du på klient projektet i **Solution Explorer** och väljer **Hantera NuGet-paket**. Välj noden **installerade paket** och skriv W**indowsAzure. caching** i rutan Sök i installerade paket. Välj **Windows** **Azure-cache** (eller **Windows** **Azure-cachelagring** beroende på versionen av NuGet-paketet), klicka på **Avinstallera**och klicka sedan på **Stäng**.

![Avinstallera Azure Managed Cache Service NuGet-paket](./media/cache-migrate-to-redis/IC757666.jpg)

Om du avinstallerar Managed Cache Service NuGet-paketet tas Managed Cache Service sammansättningar och Managed Cache Service poster i klient programmet app.config eller web.config. Eftersom vissa anpassade inställningar inte kan tas bort när du avinstallerar NuGet-paketet öppnar du web.config eller app.config och kontrollerar att följande element tas bort.

Se till att `dataCacheClients` posten tas bort från `configSections` elementet. Ta inte bort hela `configSections` elementet. ta bara bort `dataCacheClients` posten om det finns.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Kontrol lera att `dataCacheClients` avsnittet har tagits bort. `dataCacheClients`Avsnittet ser ut ungefär som i följande exempel.

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

När Managed Cache Service-konfigurationen har tagits bort kan du konfigurera cache-klienten enligt beskrivningen i följande avsnitt.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Konfigurera en cache-klient med hjälp av StackExchange. Redis NuGet-paketet
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrera Managed Cache Services kod
API: et för StackExchange. Azure-cache för Redis-klienten liknar Managed Cache Service. Det här avsnittet innehåller en översikt över skillnaderna.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ansluta till cachen med klassen ConnectionMultiplexer
I Managed Cache Service hanterades anslutningar till cachen av `DataCacheFactory` `DataCache` klasserna och. I Azure cache för Redis hanteras dessa anslutningar av `ConnectionMultiplexer` klassen.

Lägg till följande using-uttryck högst upp i en fil som du vill ha åtkomst till i cacheminnet.

```csharp
using StackExchange.Redis
```

Om det här namn området inte matchar kontrollerar du att du har lagt till StackExchange. Redis NuGet-paketet enligt beskrivningen i [snabb start: Använd Azure cache för Redis med ett .NET-program](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Observera att StackExchange. Redis-klienten kräver .NET Framework 4 eller senare.
> 
> 

Om du vill ansluta till en Azure-cache för Redis-instansen anropar du den statiska `ConnectionMultiplexer.Connect` metoden och skickar den till slut punkten och nyckeln. En metod för att dela en `ConnectionMultiplexer`-instans i ditt program är att ha en statisk egenskap som returnerar en ansluten instans, ungefär som i följande exempel. Den här metoden ger ett tråd säkert sätt att initiera en enda ansluten `ConnectionMultiplexer` instans. I det här exemplet `abortConnect` är inställt på false, vilket innebär att anropet lyckas även om det inte upprättas någon anslutning till cacheminnet. En viktig egenskap i `ConnectionMultiplexer` är att anslutningen återställs automatiskt till cachen när nätverksproblemet eller andra fel är lösta.

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

Cache-slutpunkt, nycklar och portar kan hämtas från **Azure-cachen för Redis** -bladet för din cache-instans. Mer information finns i [Azure cache för Redis-egenskaper](cache-configure.md#properties).

När anslutningen har upprättats returnerar du en referens till Azure cache för Redis-databasen genom att anropa `ConnectionMultiplexer.GetDatabase` metoden. Det objekt som returneras från `GetDatabase`-metoden är ett förenklat genomströmningsobjekt som inte behöver lagras.

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

StackExchange. Redis-klienten använder `RedisKey` typerna och `RedisValue` för att komma åt och lagra objekt i cacheminnet. Dessa typer mappar till de flesta primitiva språk typer, inklusive sträng, och används ofta inte direkt. Redis-strängar är de mest grundläggande typerna av Redis-värde och kan innehålla flera typer av data, inklusive serialiserade binära data strömmar och även om du inte kan använda typen direkt, använder du metoder som innehåller `String` i namnet. För de flesta primitiva data typer lagrar och hämtar du objekt från cachen med hjälp av `StringSet` `StringGet` -och-metoderna, om du inte lagrar samlingar eller andra Redis-datatyper i cacheminnet. 

`StringSet`och `StringGet` liknar Managed Cache service `Put` och `Get` metoder, med en större skillnad innan du ställer in och hämtar ett .net-objekt i cachen måste du först serialisera det. 

Vid anrop returneras `StringGet` det om objektet finns, och om det inte, returneras null. I så fall kan du hämta värdet från önskad data källa och lagra det i cacheminnet för senare användning. Det här mönstret kallas för cache-undan-mönstret.

Ange förfallodatum för ett objekt i cacheminnet med hjälp av `TimeSpan`-parametern för `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure cache för Redis kan arbeta med .NET-objekt och primitiva data typer, men innan ett .NET-objekt kan cachelagras måste det serialiseras. Den här serialiseringen är ansvaret för programutvecklaren och ger flexibilitet för utvecklare i valet av serialiseraren. Mer information och exempel kod finns i [arbeta med .net-objekt i cacheminnet](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrera ASP.NET-sessionstillstånd och cachelagring av utdata till Azure cache för Redis
Azure cache för Redis har providrar för både ASP.NET och cachelagring av utdata. Om du vill migrera ditt program som använder Managed Cache Service versioner av dessa providers måste du först ta bort de befintliga avsnitten från web.config och sedan konfigurera Azure-cachen för Redis-versioner av providern. Anvisningar om hur du använder Azure cache för Redis ASP.NET-providers finns i [ASP.net session State Provider för Azure cache för Redis](cache-aspnet-session-state-provider.md) och [ASP.net Provider för utgående cache för Azure cache för Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Nästa steg
Utforska [Azure cache för Redis-dokumentation](https://azure.microsoft.com/documentation/services/cache/) för självstudier, exempel, videor med mera.

