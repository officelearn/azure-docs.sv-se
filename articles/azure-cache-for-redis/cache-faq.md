---
title: Vanliga frågor och svar om Azure Cache for Redis
description: Lär dig svaren på vanliga frågor, mönster och metodtips för Azure Cache för Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 6ba292850c057284fff265c8a77386d21374942a
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010230"
---
# <a name="azure-cache-for-redis-faq"></a>Vanliga frågor och svar om Azure Cache for Redis
Lär dig svaren på vanliga frågor, mönster och metodtips för Azure Cache för Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Tänk om min fråga inte besvaras här?
Om din fråga inte finns med här, låt oss veta så hjälper vi dig att hitta ett svar.

* Du kan ställa en fråga i kommentarerna i slutet av den här vanliga frågor och svar och samarbeta med Azure Cache-teamet och andra gruppmedlemmar om den här artikeln.
* För att nå en bredare målgrupp kan du ställa en fråga på [Azure Cache MSDN Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) och samarbeta med Azure Cache-teamet och andra medlemmar i communityn.
* Om du vill göra en funktionsbegäran kan du skicka in dina begäranden och idéer till [Azure Cache för Redis User Voice](https://feedback.azure.com/forums/169382-cache).
* Du kan också skicka ett e-postmeddelande till oss på Extern feedback i [Azure Cache](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Grunderna i Azure Cache för Redis
Vanliga frågor och frågor i det här avsnittet täcker några av grunderna i Azure Cache för Redis.

* [Vad är Azure Cache for Redis?](#what-is-azure-cache-for-redis)
* [Hur kommer jag igång med Azure Cache för Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Följande vanliga frågor och svar omfattar grundläggande begrepp och frågor om Azure Cache för Redis och besvaras i ett av de andra vanliga frågor och svar.

* [Vad Azure Cache för Redis erbjuder och storlek ska jag använda?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Vilken Azure Cache för Redis-klienter kan jag använda?](#what-azure-cache-for-redis-clients-can-i-use)
* [Finns det en lokal emulator för Azure Cache för Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hur övervakar jag hälso- och prestanda för min cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Planera vanliga frågor
* [Vad Azure Cache för Redis erbjuder och storlek ska jag använda?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure Cache för Redis-prestanda](#azure-cache-for-redis-performance)
* [I vilken region ska jag hitta min cache?](#in-what-region-should-i-locate-my-cache)
* [Hur faktureras jag för Azure Cache för Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Kan jag använda Azure Cache för Redis med Azure Government Cloud, Azure China Cloud eller Microsoft Azure Germany?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Vanliga frågor om utveckling
* [Vad gör konfigurationsalternativen StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Vilken Azure Cache för Redis-klienter kan jag använda?](#what-azure-cache-for-redis-clients-can-i-use)
* [Finns det en lokal emulator för Azure Cache för Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hur kör jag Redis-kommandon?](#how-can-i-run-redis-commands)
* [Varför har inte Azure Cache för Redis en MSDN-klassbiblioteksreferens som några av de andra Azure-tjänsterna?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Kan jag använda Azure Cache för Redis som en PHP-sessionscache?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Vad är Redis-databaser?](#what-are-redis-databases)

## <a name="security-faqs"></a>Vanliga frågor om säkerhet
* [När ska jag aktivera icke-TLS/SSL-porten för anslutning till Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Vanliga frågor om produktion
* [Vilka är några bästa metoder för produktion?](#what-are-some-production-best-practices)
* [Vilka är några av de överväganden när du använder vanliga Redis-kommandon?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hur kan jag jämföra och testa prestanda för min cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Viktiga detaljer om ThreadPool-tillväxt](#important-details-about-threadpool-growth)
* [Aktivera server GC för att få mer dataflöde på klienten när du använder StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Prestandaöverväganden kring anslutningar](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Övervaka och felsöka vanliga frågor
Vanliga frågor och svar i det här avsnittet täcker vanliga övervaknings- och felsökningsfrågor. Mer information om hur du övervakar dina Azure-cache för Redis-instanser finns i [Så här övervakar du Azure Cache för Redis](cache-how-to-monitor.md) och de olika felsökningsguiderna.

* [Hur övervakar jag hälso- och prestanda för min cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Varför ser jag timeout?](#why-am-i-seeing-timeouts)
* [Varför kopplades min klient från cacheminnet?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Vanliga frågor om tidigare cache-erbjudanden
* [Vilket Azure Cache-erbjudande är rätt för mig?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Vad är Azure Cache for Redis?
Azure Cache för Redis är baserad på den populära programvaran [redis](https://redis.io/)med öppen källkod. Det ger dig tillgång till en säker, dedikerad Azure Cache för Redis, som hanteras av Microsoft och är tillgänglig från alla program i Azure. En mer detaljerad översikt finns på produktsidan [för Azure Cache for Redis](https://azure.microsoft.com/services/cache/) på Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hur kommer jag igång med Azure Cache för Redis?
Det finns flera sätt att komma igång med Azure Cache för Redis.

* Du kan kolla in en av våra tutorials tillgängliga för [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)och [Python](cache-python-get-started.md).
* Du kan titta på [Hur du skapar högpresterande appar med Microsoft Azure Cache för Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Du kan läsa klientdokumentationen för de klienter som matchar projektets utvecklingsspråk för att se hur du använder Redis. Det finns många Redis-klienter som kan användas med Azure Cache för Redis. En lista över Redis-klienter finns i [https://redis.io/clients](https://redis.io/clients).

Om du inte redan har ett Azure-konto kan du:

* [Öppna ett Azure-konto kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Du får kredit som kan användas för att prova Azure-tjänster som normalt inte är kostnadsfria. Du kan behålla kontot även efter att krediten är slut och använda kostnadsfria Azure-tjänster och -funktioner.
* [Aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Vad Azure Cache för Redis erbjuder och storlek ska jag använda?
Varje Azure-cache för Redis-erbjudande ger olika nivåer av **storlek,** **bandbredd,** **hög tillgänglighet**och **SLA-alternativ.**

Följande är överväganden för att välja ett cacheerbjudande.

* **Minne:** Basic- och Standardnivåerna erbjuder 250 MB – 53 GB. Premium-nivån erbjuder upp till 1,2 TB (som ett kluster) eller 120 GB (icke-klustrade). Mer information finns i [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Nätverksprestanda:** Om du har en arbetsbelastning som kräver högt dataflöde erbjuder Premium-nivån mer bandbredd jämfört med Standard eller Basic. Även inom varje nivå, större storlek cachar har mer bandbredd på grund av den underliggande virtuella datorn som är värd för cachen. Mer information finns i [följande tabell](#cache-performance).
* **Dataflöde:** Premium-nivån erbjuder maximalt tillgängligt dataflöde. Om cacheservern eller klienten når bandbreddsgränserna kan du få timeout på klientsidan. Se tabellen nedan för mer information.
* **Hög tillgänglighet/SLA:** Azure Cache för Redis garanterar att en Standard/Premium-cache är tillgänglig minst 99,9 % av tiden. Mer information om vårt SLA finns i [Azure Cache for Redis Pricing](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). SLA täcker endast anslutningen till cacheslutpunkterna. Serviceavtalet omfattar inte skydd mot dataförlust. Vi rekommenderar att du använder Redis-funktionen för beständighet i Premium-nivån för att öka återhämtningen mot dataförlust.
* **Redis Data Persistens:** Premium-nivån gör att du kan spara cachedata i ett Azure Storage-konto. I en Basic/Standard-cache lagras alla data endast i minnet. Underliggande infrastrukturproblem kan leda till potentiell dataförlust. Vi rekommenderar att du använder Redis-funktionen för beständighet i Premium-nivån för att öka återhämtningen mot dataförlust. Azure Cache för Redis erbjuder RDB och AOF (kommer snart) alternativ i Redis persistens. Mer information finns i [Så här konfigurerar du persistens för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).
* **Redis Cluster**: Om du vill skapa cacheminnen som är större än 120 GB, eller för att fragmentera data över flera Redis-noder, kan du använda Redis-klustring, som är tillgängligt på Premium-nivån. Varje nod består av ett primärt/replikcachepar för hög tillgänglighet. Mer information finns i [Så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).
* **Förbättrad säkerhet och nätverksisolering:** Azure Virtual Network (VNET) distribution ger förbättrad säkerhet och isolering för din Azure Cache för Redis, samt undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten. Mer information finns i [Så här konfigurerar du stöd för virtuellt nätverk för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md).
* **Konfigurera Redis:** I både standard- och Premium-nivåerna kan du konfigurera Redis för Keyspace-meddelanden.
* **Maximalt antal klientanslutningar**: Premium-nivån erbjuder det maximala antalet klienter som kan ansluta till Redis, med ett högre antal anslutningar för större cacheminnen. Klustring ökar inte antalet tillgängliga anslutningar för en klustercachen. Mer information finns i [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Dedikerad kärna för Redis Server:** I Premium-nivån har alla cachestorlekar en dedikerad kärna för Redis. På basic/standard-nivåerna har C1-storleken och högre en dedikerad kärna för Redis-servern.
* **Redis är entrådad** så att ha mer än två kärnor ger inte ytterligare fördelar jämfört med att bara ha två kärnor, men större VM-storlekar har vanligtvis mer bandbredd än mindre storlekar. Om cacheservern eller klienten når bandbreddsgränserna får du timeout på klientsidan.
* **Prestandaförbättringar:** Cacheminnen på Premium-nivån distribueras på maskinvara som har snabbare processorer, vilket ger bättre prestanda jämfört med basic- eller standardnivån. Premium-nivåcachar har högre dataflöde och lägre svarstider.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure Cache för Redis-prestanda
Följande tabell visar de maximala bandbreddsvärden som observerats när `redis-benchmark.exe` du testar olika storlekar av standard- och premiumcachen med hjälp av en IaaS-vm mot Slutpunkten Azure Cache för Redis. För TLS-dataflöde används redis-benchmark med stunnel för att ansluta till Slutpunkten För Azure Cache för Redis.

>[!NOTE] 
>Dessa värden är inte garanterade och det finns inget serviceavtal för dessa tal, men bör vara typiska. Du bör läsa in testa ditt eget program för att bestämma rätt cachestorlek för ditt program.
>Dessa siffror kan ändras när vi publicerar nyare resultat med jämna mellanrum.
>

Av denna tabell kan vi dra följande slutsatser:

* Dataflöde för cacheminnen som har samma storlek är högre på Premium-nivån jämfört med standardnivån. Med en 6 GB Cache är till exempel dataflödet för P1 180 000 begäranden per sekund (RPS) jämfört med 100 000 RPS för C3.
* Med Redis-klustring ökar dataflödet linjärt när du ökar antalet shards (noder) i klustret. Om du till exempel skapar ett P4-kluster med 10 shards är det tillgängliga dataflödet 400 000 * 10 = 4 miljoner RPS.
* Dataflödet för större nyckelstorlekar är högre på Premium-nivån jämfört med standardnivån.

| Prisnivå | Storlek | Processorkärnor | Tillgänglig bandbredd | Värdestorlek på 1 KB | Värdestorlek på 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Standardchartstorlekar** | | |**Megabit per sekund (Mb/s) / Megabyte per sekund (MB/s)** |**Begäranden per sekund (RPS) icke-SSL** |**Begäranden per sekund (RPS) SSL** |
| CO | 250 MB | Delad | 100 / 12.5  |  15 000 |   7 500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 (på andra sätt) |   6 GB | 4      | 1000 / 125  | 100 000 |  90 000 |
| C4 (på andra sätt) |  13 GB | 2      | 500 / 62.5  |  60 000 |  55,000 |
| C5 (på andra sätt) |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 (på andra sätt) |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Premium cache storlekar** | |**CPU-kärnor per shard** | **Megabit per sekund (Mb/s) / Megabyte per sekund (MB/s)** |**Begäranden per sekund (RPS) icke-SSL, per shard** |**Begäranden per sekund (RPS) SSL, per shard** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

Instruktioner om hur du konfigurerar stunnel eller hämtar `redis-benchmark.exe`Redis-verktygen, till exempel , finns i avsnittet [Hur kör jag Redis-kommandon?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>I vilken region ska jag hitta min cache?
För bästa prestanda och lägsta svarstid, hitta din Azure-cache för Redis i samma region som cacheklientprogrammet.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Hur faktureras jag för Azure Cache för Redis?
Azure Cache för Redis prissättning är [här](https://azure.microsoft.com/pricing/details/cache/). På prissidan visas priser som en timtaxa. Cacheminnen faktureras per minut från den tidpunkt då cacheminnet skapas tills den tidpunkt då en cache tas bort. Det finns inget alternativ för att stoppa eller pausa faktureringen av en cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Kan jag använda Azure Cache för Redis med Azure Government Cloud, Azure China Cloud eller Microsoft Azure Germany?
Ja, Azure Cache för Redis är tillgängligt i Azure Government Cloud, Azure China 21Vianet Cloud och Microsoft Azure Germany. Url:erna för åtkomst till och hantering av Azure Cache för Redis är olika i dessa moln jämfört med Azure Public Cloud.

| Molnet   | Dns Suffix för Redis            |
|---------|---------------------------------|
| Offentlig  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Tyskland | *.redis.cache.cloudapi.de       |
| Kina   | *.redis.cache.chinacloudapi.cn  |

Mer information om överväganden när du använder Azure Cache för Redis med andra moln finns i följande länkar.

- [Azure Government-databaser – Azure Cache för Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud – Azure Cache för Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)

Information om hur du använder Azure Cache för Redis med PowerShell i Azure Government Cloud, Azure China 21Vianet Cloud och Microsoft Azure Germany finns i [Så här ansluter du till andra moln - Azure Cache för Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Vad gör konfigurationsalternativen StackExchange.Redis?
StackExchange.Redis har många alternativ. I det här avsnittet beskrivs några av de vanliga inställningarna. Mer detaljerad information om Alternativen StackExchange.Redis finns i [StackExchange.Redis-konfigurationen](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Beskrivning | Rekommendation |
| --- | --- | --- |
| AbortOnConnectFail |När den är inställd på true återansluts inte anslutningen efter ett nätverksfel. |Ställ in på false och låt StackExchange.Redis återansluta automatiskt. |
| ConnectRetry |Antalet gånger för att upprepa anslutningsförsök under den första anslutningen. |Mer information finns i följande anvisningar. |
| ConnectTimeout |Timeout i ms för anslutningsåtgärder. |Mer information finns i följande anvisningar. |

Vanligtvis är klientens standardvärden tillräckliga. Du kan finjustera alternativen baserat på din arbetsbelastning.

* **Antal försök**
  * För ConnectRetry och ConnectTimeout är den allmänna vägledningen att misslyckas snabbt och försöka igen. Den här vägledningen baseras på din arbetsbelastning och hur lång tid det tar för klienten att utfärda ett Redis-kommando och få ett svar.
  * Låt StackExchange.Redis automatiskt återansluta i stället för att kontrollera anslutningsstatus och återansluta dig själv. **Undvik att använda egenskapen ConnectionMultiplexer.IsConnected**.
  * Snöboll - ibland kan du stöta på ett problem där du försöker igen och återförsök snöboll och aldrig återhämtar sig. Om snöbollskastning inträffar bör du överväga att använda en exponentiell bakåtsträvningsalgoritm som beskrivs i [Den allmänna vägledningen för återförsök](../best-practices-retry-general.md) som publiceras av gruppen Microsoft Patterns & Practices.
  
* **Timeout-värden**
  * Tänk på din arbetsbelastning och ange värdena därefter. Om du lagrar stora värden anger du tidsgränsen till ett högre värde.
  * Ställ `AbortOnConnectFail` in på false och låt StackExchange.Redis återansluta åt dig.
  * Använd en enda ConnectionMultiplexer-instans för programmet. Du kan använda en LazyConnection för att skapa en enskild förekomst som returneras av en anslutningsegenskap, som visas i [Anslut till cachen med klassen ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ange `ConnectionMultiplexer.ClientName` egenskapen till ett unikt namn för en appinstans för diagnostik.
  * Använd `ConnectionMultiplexer` flera instanser för anpassade arbetsbelastningar.
      * Du kan följa den här modellen om du har varierande belastning i ditt program. Ett exempel:
      * Du kan ha en multiplexer för att hantera stora nycklar.
      * Du kan ha en multiplexer för att hantera små nycklar.
      * Du kan ange olika värden för tidsutgångar för anslutningar och logik för återförsök för varje ConnectionMultiplexer som du använder.
      * Ange `ClientName` egenskapen på varje multiplexer för att hjälpa till med diagnostik.
      * Den här vägledningen kan leda `ConnectionMultiplexer`till mer strömlinjeformad latens per .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Vilken Azure Cache för Redis-klienter kan jag använda?
En av de bästa sakerna med Redis är att det finns många kunder som stöder många olika utvecklingsspråk. En aktuell lista över klienter finns i [Redis-klienter](https://redis.io/clients). Självstudier som täcker flera olika språk och klienter finns i [Så här använder du Azure Cache för Redis](cache-dotnet-how-to-use-azure-redis-cache.md) och det är syskonartiklar i innehållsförteckningen.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Finns det en lokal emulator för Azure Cache för Redis?
Det finns ingen lokal emulator för Azure Cache för Redis, men du kan köra MSOpenTech-versionen av redis-server.exe från [Redis kommandoradsverktyg](https://github.com/MSOpenTech/redis/releases/) på din lokala dator och ansluta till den för att få en liknande upplevelse som en lokal cacheemulator, som visas i följande exempel:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Du kan också konfigurera en [redis.conf-fil](https://redis.io/topics/config) så att de bättre matchar [standardcacheinställningarna](cache-configure.md#default-redis-server-configuration) för din online Azure-cache för Redis om så önskas.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hur kör jag Redis-kommandon?
Du kan använda något av kommandona som visas på [Redis-kommandon](https://redis.io/commands#) förutom de kommandon som visas på [Redis-kommandon som inte stöds i Azure Cache för Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Du har flera alternativ för att köra Redis-kommandon.

* Om du har en Standard- eller Premium-cache kan du köra Redis-kommandon med [Redis-konsolen](cache-configure.md#redis-console). Redis-konsolen är ett säkert sätt att köra Redis-kommandon i Azure-portalen.
* Du kan också använda redis-kommandoradsverktygen. Så här använder du dem:
* Hämta [Redis kommandoradsverktyg](https://github.com/MSOpenTech/redis/releases/).
* Anslut till cacheminnet med `redis-cli.exe`. Passera i cacheslutpunkten med växeln -h och nyckeln med -a som visas i följande exempel:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redis-kommandoradsverktyg fungerar inte med TLS-porten, men du kan `stunnel` använda ett verktyg som att på ett säkert sätt ansluta verktygen till TLS-porten genom att följa anvisningarna i [kommandoradsverktyget För Hur du använder Kommandot line för Redis med Azure Cache för Redis.](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool)
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Varför har inte Azure Cache för Redis en MSDN-klassbiblioteksreferens som några av de andra Azure-tjänsterna?
Microsoft Azure Cache for Redis är baserad på populära Azure Cache for Redis med öppen källkod. Den kan nås av en mängd olika [Redis-klienter](https://redis.io/clients) för många programmeringsspråk. Varje klient har sitt eget API som anropar Azure Cache för Redis-instans med [Redis-kommandon](https://redis.io/commands).

Eftersom varje klient är olika finns det inte en centraliserad klassreferens på MSDN och varje klient har sin egen referensdokumentation. Förutom referensdokumentationen finns det flera självstudier som visar hur du kommer igång med Azure Cache för Redis med olika språk och cacheklienter. Information om hur du kommer åt dessa självstudier finns i [Så här använder du Azure Cache för Redis](cache-dotnet-how-to-use-azure-redis-cache.md) och det är syskonartiklar i innehållsförteckningen.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Kan jag använda Azure Cache för Redis som en PHP-sessionscache?
Ja, om du vill använda Azure Cache för Redis som en PHP-sessionscache anger du anslutningssträngen till Azure Cache för Redis-instans i `session.save_path`.

> [!IMPORTANT]
> När du använder Azure Cache för Redis som en PHP-sessionscache måste du URL koda säkerhetsnyckeln som används för att ansluta till cacheminnet, vilket visas i följande exempel:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Om nyckeln inte är URL-kodad kan du få ett undantag med ett meddelande som:`Failed to parse session.save_path`
>
>

Mer information om hur du använder Azure Cache för Redis som en PHP-sessionscache med PhpRedis-klienten finns i [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Vad är Redis-databaser?

Redis-databaser är bara en logisk separation av data inom samma Redis-instans. Cacheminnet delas mellan alla databaser och den faktiska minnesförbrukningen i en viss databas beror på vilka nycklar/värden som lagras i databasen. En C6-cache har till exempel 53 GB minne och en P5 har 120 GB. Du kan välja att lägga alla 53 GB / 120 GB i en databas eller så kan du dela upp det mellan flera databaser. 

> [!NOTE]
> När du använder en Premium Azure Cache för Redis med kluster aktiverat är endast databas 0 tillgänglig. Den här begränsningen är en inneboende Redis-begränsning och är inte specifik för Azure Cache för Redis. Mer information finns i [Måste jag göra några ändringar i klientprogrammet för att använda klustring?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>När ska jag aktivera icke-TLS/SSL-porten för anslutning till Redis?
Redis-servern stöder inte TLS, men Azure Cache för Redis gör det. Om du ansluter till Azure Cache för Redis och din klient stöder TLS, till exempel StackExchange.Redis, bör du använda TLS.

>[!NOTE]
>Icke-TLS-porten är inaktiverad som standard för nya Azure-cache för Redis-instanser. Om klienten inte stöder TLS måste du aktivera icke-TLS-porten genom att följa anvisningarna i avsnittet [Access-portar](cache-configure.md#access-ports) i artikeln [Konfigurera en cache i Azure Cache för Redis.](cache-configure.md)
>
>

Redis verktyg `redis-cli` som inte fungerar med TLS-porten, men du `stunnel` kan använda ett verktyg som att säkert ansluta verktygen till TLS-porten genom att följa anvisningarna i [Announcing ASP.NET Session State Provider för Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogginlägg.

Instruktioner om hur du laddar ned Redis-verktygen finns i avsnittet [Hur kör jag Redis-kommandon?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>Vilka är några bästa metoder för produktion?
* [Metodtips för StackExchange.Redis](#stackexchangeredis-best-practices)
* [Konfiguration och koncept](#configuration-and-concepts)
* [Prestandatestning](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Metodtips för StackExchange.Redis
* Ställ `AbortConnect` in på false och låt sedan ConnectionMultiplexer återansluta automatiskt. [Se här för mer information](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Återanvänd ConnectionMultiplexer - skapa inte en ny för varje begäran. Mönstret `Lazy<ConnectionMultiplexer>` [som visas här](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) rekommenderas.
* Redis fungerar bäst med mindre värden, så överväg att hacka upp större data i flera nycklar. I [denna Redis-diskussion](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)anses 100 kB vara stor. Läs [den här artikeln](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) för ett exempel problem som kan orsakas av stora värden.
* Konfigurera [threadpool-inställningarna](#important-details-about-threadpool-growth) för att undvika timeout.
* Använd minst standardanslutningTimeout på 5 sekunder. Detta intervall ger StackExchange.Redis tillräckligt med tid för att återupprätta anslutningen i händelse av ett nätverk blip.
* Var medveten om prestandakostnaderna i samband med olika åtgärder som du kör. `KEYS` Kommandot är till exempel en O(n)-åtgärd och bör undvikas. Den [redis.io platsen](https://redis.io/commands/) har information om tidskomplexiteten för varje åtgärd som den stöder. Klicka på varje kommando om du vill se komplexiteten för varje åtgärd.

#### <a name="configuration-and-concepts"></a>Konfiguration och koncept
* Använd standard- eller premiumnivå för produktionssystem. Basic-nivån är ett system med en nod utan datareplikering och serviceavtal. Använd också minst ett C1-cacheminne. C0-cacheminnen används vanligtvis för enkla utvecklings-/testscenarier.
* Kom ihåg att Redis är ett **datalager i minnet.** Läs [den här artikeln](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) så att du är medveten om scenarier där dataförlust kan uppstå.
* Utveckla ditt system så att det kan hantera anslutning blips [på grund av patchning och redundans](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Prestandatestning
* Börja med `redis-benchmark.exe` att använda för att få en känsla för eventuellt dataflöde innan du skriver dina egna perf tester. Eftersom `redis-benchmark` det inte stöder TLS måste du [aktivera icke-TLS-porten via Azure-portalen](cache-configure.md#access-ports) innan du kör testet. Exempel på exempel finns i [Hur kan jag jämföra och testa prestanda för min cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Klientens virtuella dator som används för testning bör finnas i samma region som azure-cache för Redis-instans.
* Vi rekommenderar att du använder Dv2 VM-serien för din klient eftersom de har bättre hårdvara och bör ge bästa resultat.
* Kontrollera att klientens virtuella datorsystem du väljer har minst lika mycket dator- och bandbreddskapacitet som cachen du testar.
* Aktivera VRSS på klientmaskinen om du använder Windows. [Se här för mer information](https://technet.microsoft.com/library/dn383582.aspx).
* Redis-instanser på Premium-nivå har bättre nätverksfördröjning och dataflöde eftersom de körs på bättre maskinvara för både CPU och Nätverk.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Vilka är några av de överväganden när du använder vanliga Redis-kommandon?

* Undvik att använda vissa Redis-kommandon som tar lång tid att slutföra, såvida du inte förstår effekten av dessa kommandon fullt ut. Kör till exempel inte kommandot [KEYS](https://redis.io/commands/keys) i produktionen. Beroende på antalet nycklar kan det ta lång tid att returnera. Redis är en server med en tråd och bearbetar kommandon en i taget. Om du har andra kommandon utfärdade efter NYCKLAR bearbetas de inte förrän Kommandot Redis bearbetar kommandot KEYS. Den [redis.io platsen](https://redis.io/commands/) har information om tidskomplexiteten för varje åtgärd som den stöder. Klicka på varje kommando om du vill se komplexiteten för varje åtgärd.
* Nyckelstorlekar - ska jag använda små nyckel/värden eller stora nyckel/värden? Det beror på scenariot. Om scenariot kräver större nycklar kan du justera ConnectionTimeout och sedan försöka igen värden och justera logiken för återförsök. Ur ett Redis-serverperspektiv ger mindre värden bättre prestanda.
* Dessa överväganden innebär inte att du inte kan lagra större värden i Redis. du måste vara medveten om följande överväganden. Svarstider kommer att vara högre. Om du har en uppsättning data som är större och en som är mindre, kan du använda flera ConnectionMultiplexer-instanser, var och en konfigurerad med en annan uppsättning timeout- och återförsöksvärden, enligt beskrivningen i föregående [Konfigurationsalternativ för StackExchange.Redis](#cache-configuration) gör.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hur kan jag jämföra och testa prestanda för min cache?
* [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet. Du kan visa måtten i Azure-portalen och du kan också [ladda ned och granska](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem med de verktyg du väljer.
* Du kan använda redis-benchmark.exe för att läsa in test din Redis-server.
* Kontrollera att lasttestningsklienten och Azure Cache för Redis finns i samma region.
* Använd redis-cli.exe och övervaka cacheminnet med kommandot INFO.
* Om belastningen orsakar hög minnesfragmentering bör du skala upp till en större cachestorlek.
* Instruktioner om hur du laddar ned Redis-verktygen finns i avsnittet [Hur kör jag Redis-kommandon?](#cache-commands)

Följande kommandon ger ett exempel på hur du använder redis-benchmark.exe. Om du vill ha korrekta resultat kör du dessa kommandon från en virtuell dator i samma region som cacheminnet.

* Testa Pipelined SET-begäranden med en 1k-nyttolast

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testa Pipelined GET-begäranden med en 1k nyttolast.
  OBS: Kör SET-testet som visas ovan först för att fylla cache

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Viktiga detaljer om ThreadPool-tillväxt
CLR ThreadPool har två typer av trådar - "Worker" och "I/O Completion Port" (IOCP) trådar.

* Arbetartrådar används för saker `Task.Run(…)`som `ThreadPool.QueueUserWorkItem(…)` att bearbeta eller metoder. Dessa trådar används också av olika komponenter i CLR när arbetet måste ske på en bakgrundstråd.
* IOCP-trådar används när asynkron I/O.a händer, till exempel när du läser från nätverket.

Trådpoolen tillhandahåller nya arbetstrådar eller I/O-kompletteringstrådar på begäran (utan begränsning) tills den når inställningen "Minimum" för varje typ av tråd. Som standard är det minsta antalet trådar inställt på antalet processorer i ett system.

När antalet befintliga (upptagna) trådar träffar det "minsta" antalet trådar, kommer ThreadPool att begränsa hastigheten med vilken den injicerar nya trådar till en tråd per 500 millisekunder. Vanligtvis, om ditt system får en explosion av arbete som behöver en IOCP tråd, kommer det att bearbeta att fungera snabbt. Men om explosionen av arbete är mer än den konfigurerade "Minimum" inställningen, kommer det att finnas en viss fördröjning i bearbetningen av en del av arbetet som ThreadPool väntar på en av två saker att hända.

1. En befintlig tråd blir gratis att bearbeta arbetet.
2. Ingen befintlig tråd blir gratis för 500 ms, så en ny tråd skapas.

I grund och botten innebär det att när antalet upptagna trådar är större än Min trådar, är du sannolikt betalar en 500-ms fördröjning innan nätverkstrafik bearbetas av programmet. Dessutom är det viktigt att notera att när en befintlig tråd förblir inaktiv längre än 15 sekunder (baserat på vad jag minns), kommer det att rensas upp och denna cykel av tillväxt och krympning kan upprepas.

Om vi tittar på ett exempel felmeddelande från StackExchange.Redis (version 1.0.450 eller senare), kommer du att se att det nu skriver ut ThreadPool statistik (se IOCP och WORKER detaljer nedan).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

I föregående exempel kan du se att för IOCP tråd finns det sex upptagna trådar och systemet är konfigurerat för att tillåta fyra minsta trådar. I det här fallet skulle klienten sannolikt ha sett två 500-ms förseningar, eftersom 6 > 4.

Observera att StackExchange.Redis kan slå timeout om tillväxten av antingen IOCP- eller WORKER-trådar begränsas.

### <a name="recommendation"></a>Rekommendation

Med tanke på den här informationen rekommenderar vi starkt att kunderna ställer in det lägsta konfigurationsvärdet för IOCP- och WORKER-trådar till något större än standardvärdet. Vi kan inte ge en storlek passar alla vägledning om vad detta värde bör vara eftersom rätt värde för ett program kommer sannolikt att vara för hög eller låg för ett annat program. Den här inställningen kan också påverka prestanda för andra delar av komplicerade program, så varje kund måste finjustera den här inställningen till sina specifika behov. En bra startplats är 200 eller 300, sedan testa och justera efter behov.

Konfigurera den här inställningen:

* Vi rekommenderar att du ändrar den här inställningen programmässigt med hjälp `global.asax.cs`av metoden [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) i . Ett exempel:

```cs
private readonly int minThreads = 200;
void Application_Start(object sender, EventArgs e)
{
    // Code that runs on application startup
    AreaRegistration.RegisterAllAreas();
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    BundleConfig.RegisterBundles(BundleTable.Bundles);
    ThreadPool.SetMinThreads(minThreads, minThreads);
}
```

  > [!NOTE]
  > Värdet som anges med den här metoden är en global inställning som påverkar hela AppDomain. Om du till exempel har en 4-kärnig maskin och vill ställa in *minWorkerThreads* och *minIoThreads* till 50 per CPU under körning, använder du **ThreadPool.SetMinThreads(200, 200)**.

* Det är också möjligt att ange inställningen för minsta trådar med hjälp av [ *konfigurationsinställningen minIoThreads* eller *minWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) under `<processModel>` konfigurationselementet i `Machine.config`, vanligtvis finns på `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`. **Det rekommenderas i allmänhet inte att ange antalet minsta trådar på detta sätt, eftersom det är en systemomfattande inställning.**

  > [!NOTE]
  > Värdet som anges i det här konfigurationselementet är en inställning *per kärna.* Om du till exempel har en 4-kärnig maskin och vill att inställningen *minioThreads* `<processModel minIoThreads="50"/>`ska vara 200 vid körning, använder du .
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Aktivera server GC för att få mer dataflöde på klienten när du använder StackExchange.Redis
Aktivera server GC kan optimera klienten och ge bättre prestanda och dataflöde när du använder StackExchange.Redis. Mer information om server GC och hur du aktiverar den finns i följande artiklar:

* [Så här aktiverar du server GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Grunderna i sophämtning](/dotnet/standard/garbage-collection/fundamentals)
* [Sophämtning och prestanda](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Prestandaöverväganden kring anslutningar

Varje prisnivå har olika gränser för klientanslutningar, minne och bandbredd. Varje storlek på cachen tillåter *upp till* ett visst antal anslutningar, men varje anslutning till Redis har omkostnader som är associerade med den. Ett exempel på sådana omkostnader skulle vara CPU och minnesanvändning som ett resultat av TLS/SSL-kryptering. Den maximala anslutningsgränsen för en viss cachestorlek förutsätter en lätt inläst cache. Om belastningen från anslutningskostnaderna *plus* belastningen från klientåtgärder överskrider kapaciteten för systemet kan cachen uppstå kapacitetsproblem även om du inte har överskridit anslutningsgränsen för den aktuella cachestorleken.

Mer information om de olika anslutningsgränserna för varje nivå finns i [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/). Mer information om anslutningar och andra standardkonfigurationer finns i [Standardkonfiguration för Redis-server](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hur övervakar jag hälso- och prestanda för min cache?
Microsoft Azure Cache för Redis-instanser kan övervakas i [Azure-portalen](https://portal.azure.com). Du kan visa mått, fästa måttdiagram på Startboard, anpassa datum- och tidsintervallet för övervakningsdiagram, lägga till och ta bort mått från diagrammen och ange aviseringar när vissa villkor uppfylls. Mer information finns i [Övervaka Azure Cache för Redis](cache-how-to-monitor.md).

Azure Cache för Redis **Resource-menyn** innehåller också flera verktyg för övervakning och felsökning av cacheminnen.

* **Diagnostisera och lösa problem** ger information om vanliga problem och strategier för att lösa dem.
* **Resurshälsan** bevakar din resurs och talar om för dig om den körs som förväntat. Mer information om hälsotjänsten för Azure Resource finns i [hälsoöversikten för Azure Resource](../resource-health/resource-health-overview.md).
* Med ny **supportbegäran** kan du öppna en supportbegäran för cacheminnet.

Med de här verktygen kan du övervaka hälsotillståndet för dina Azure-cache för Redis-instanser och hjälpa dig att hantera dina cachelagringsprogram. Mer information finns i avsnittet "Support & felsökningsinställningar" i [Så här konfigurerar du Azure Cache för Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Varför ser jag timeout?
Timeouts inträffar i klienten som du använder för att prata med Redis. När ett kommando skickas till Redis-servern ställs kommandot i kö och Redis-servern hämtar så småningom kommandot och kör det. Men klienten kan time out under denna process och om det gör ett undantag höjs på den anropssidan. Mer information om problem med felsökning av timeout finns i [felsökning](cache-troubleshoot-client.md) på klientsidan och [undantag för StackExchange.Redis.](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Varför kopplades min klient från cacheminnet?
Följande är några vanliga orsaker till att en cache kopplas från.

* Orsaker på klientsidan
  * Klientprogrammet har distribuerats om.
  * Klientprogrammet utförde en skalningsåtgärd.
    * När det gäller molntjänster eller webbappar kan detta bero på automatisk skalning.
  * Nätverkslagret på klientsidan har ändrats.
  * Tillfälliga fel uppstod i klienten eller i nätverksnoderna mellan klienten och servern.
  * Tröskelvärdena för bandbredd uppnåddes.
  * CPU-bundna åtgärder tog för lång tid att slutföra.
* Orsaker på serversidan
  * På standardcacheerbjudandet initierade Azure Cache för Redis-tjänsten en återställning från den primära noden till den sekundära noden.
  * Azure korrigering instansen där cachen distribuerades
    * Detta kan vara för Redis-serveruppdateringar eller allmänt vm-underhåll.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Vilket Azure Cache-erbjudande är rätt för mig?
> [!IMPORTANT]
> Enligt förra årets [meddelande](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)har Azure Managed Cache Service och Azure In-Role Cache-tjänsten **dragits tillbaka den** 30 november 2016. Vår rekommendation är att använda [Azure Cache för Redis](https://azure.microsoft.com/services/cache/). Information om migrering finns i [Migrera från hanterad cachetjänst till Azure Cache för Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache för Redis är allmänt tillgängligt i storlekar upp till 120 GB och har ett SLA-anslutnings-service på 99,9 %. Den nya [premiumnivån](cache-premium-tier-intro.md) erbjuder storlekar på upp till 1,2 TB och stöd för klustring, VNET och persistens, med ett serviceavtal på 99,9 %.

Azure Cache för Redis ger kunderna möjlighet att använda en säker, dedikerad Azure-cache för Redis, som hanteras av Microsoft. Med det här erbjudandet får du utnyttja den omfattande funktionsuppsättningen och ekosystemet som tillhandahålls av Redis och tillförlitlig hosting och övervakning från Microsoft.

Till skillnad från traditionella cacheminnen som endast behandlar nyckel-värde par, är Redis populär för sina högpresterande datatyper. Redis stöder också körning av atomåtgärder på dessa typer, till exempel lägga till i en sträng. öka värdet i en hash; trycka till en lista; datoranvändning satt skärningspunkt, union och skillnad; eller få den medlem med högst rang i en sorterad uppsättning. Andra funktioner inkluderar stöd för transaktioner, pub / sub, Lua skript, nycklar med en begränsad tid att leva, och konfigurationsinställningar för att göra Redis beter sig mer som en traditionell cache.

En annan viktig aspekt för Redis framgång är den friska, levande öppen källkod ekosystem byggt runt den. Detta återspeglas i den mångsidiga uppsättningen Redis-klienter som är tillgängliga på flera språk. Det här ekosystemet och ett brett antal klienter gör att Azure Cache for Redis kan användas av nästan alla arbetsbelastningar som du skulle skapa i Azure.

Mer information om hur du kommer igång med Azure Cache för Redis finns i Så här använder du [Azure Cache för Redis](cache-dotnet-how-to-use-azure-redis-cache.md) och [Azure Cache for Redis-dokumentation](index.yml).

### <a name="managed-cache-service"></a>Tjänsten Hanterad cache
[Managed Cache-tjänsten drogs tillbaka den 30 november 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Mer omarkna arkiverad dokumentation finns i [Dokumentation av Arkiverad hanterad cachetjänst](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Cache för roll
[In-Role Cache pensionerades 30 november 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Mer omarkande arkiverad dokumentation finns [i Arkiverad dokumentation av cachedokumentation i rollen](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
