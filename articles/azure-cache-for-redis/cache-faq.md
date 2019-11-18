---
title: Vanliga frågor och svar om Azure Cache for Redis
description: Lär dig mer om svar på vanliga frågor, mönster och metod tips för Azure cache för Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: yegu
ms.openlocfilehash: e497a5c54a80dbed2ea94f8251d198c1c8bc5043
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122793"
---
# <a name="azure-cache-for-redis-faq"></a>Vanliga frågor och svar om Azure Cache for Redis
Lär dig svaren på vanliga frågor, mönster och metod tips för Azure cache för Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?
Om din fråga inte visas här kan vi hjälpa oss och vi hjälper dig att hitta ett svar.

* Du kan publicera en fråga i kommentarerna i slutet av de här frågorna och engagera dig med Azure cache-teamet och andra community-medlemmar om den här artikeln.
* För att nå en bredare publik kan du publicera en fråga på [Azure-cache MSDN-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) och engagera dig med Azure cache-teamet och andra medlemmar i communityn.
* Om du vill göra en funktions förfrågan kan du skicka in dina förfrågningar och idéer till [Azure cache för Redis User Voice](https://feedback.azure.com/forums/169382-cache).
* Du kan också skicka ett e-postmeddelande till oss vid [extern Azure cache-feedback](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Azure cache för Redis-grunder
Vanliga frågor och svar i det här avsnittet beskriver några av grunderna i Azure cache för Redis.

* [Vad är Azure cache för Redis?](#what-is-azure-cache-for-redis)
* [Hur kan jag komma igång med Azure cache för Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

Följande vanliga frågor och svar gäller grundläggande begrepp och frågor om Azure cache för Redis och besvaras i något av de andra avsnitten om vanliga frågor och svar.

* [Vad ska jag använda Azure cache för Redis-erbjudande och storlek?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Vilken Azure-cache för Redis-klienter kan jag använda?](#what-azure-cache-for-redis-clients-can-i-use)
* [Finns det en lokal emulator för Azure cache för Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hur gör jag för att övervaka hälso tillstånd och prestanda för mitt cacheminne?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Planera vanliga frågor
* [Vad ska jag använda Azure cache för Redis-erbjudande och storlek?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure cache för Redis-prestanda](#azure-cache-for-redis-performance)
* [I vilken region ska jag hitta mitt cacheminne?](#in-what-region-should-i-locate-my-cache)
* [Hur faktureras jag för Azure cache för Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Kan jag använda Azure cache för Redis med Azure Government moln, Azure Kina-moln eller Microsoft Azure Tyskland?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Vanliga frågor och svar om utveckling
* [Vad är konfigurations alternativen för StackExchange. Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Vilken Azure-cache för Redis-klienter kan jag använda?](#what-azure-cache-for-redis-clients-can-i-use)
* [Finns det en lokal emulator för Azure cache för Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hur kan jag köra Redis-kommandon?](#how-can-i-run-redis-commands)
* [Varför har inte Azure cache för Redis en MSDN-klass biblioteks referens som några av de andra Azure-tjänsterna?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Kan jag använda Azure cache för Redis som ett PHP-sessionsnamn?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Vad är Redis-databaser?](#what-are-redis-databases)

## <a name="security-faqs"></a>Vanliga frågor och svar
* [När ska jag aktivera icke-SSL-porten för anslutning till Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Vanliga frågor om produktion
* [Vad är några produktions metod tips?](#what-are-some-production-best-practices)
* [Vad är några av överväganden när du använder vanliga Redis-kommandon?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hur kan jag mäta och testa prestanda för mitt cacheminne?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Viktig information om hur du växer i trådpool](#important-details-about-threadpool-growth)
* [Aktivera server-GC för att få mer data flöde på klienten när du använder StackExchange. Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Prestanda överväganden kring anslutningar](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Vanliga frågor och svar om övervakning och fel sökning
FOS i det här avsnittet beskriver vanliga frågor om övervakning och fel sökning. Mer information om övervakning och fel sökning av Azure-cache för Redis-instanser finns i [så här övervakar du Azure cache för Redis](cache-how-to-monitor.md) och de olika fel söknings guiderna.

* [Hur gör jag för att övervaka hälso tillstånd och prestanda för mitt cacheminne?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Varför ser jag timeout?](#why-am-i-seeing-timeouts)
* [Varför var min klient frånkopplad från cachen?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Vanliga frågor och svar om tidigare cache
* [Vilket Azure cache-erbjudande är bäst för mig?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Vad är Azure Cache for Redis?
Azure cache för Redis baseras på den populära program [Redis](https://redis.io/)med öppen källkod. Det ger dig åtkomst till en säker, dedikerad Azure-cache för Redis, som hanteras av Microsoft och är tillgänglig från alla program i Azure. En mer detaljerad översikt finns på produkt sidan för [Azure-cache för Redis](https://azure.microsoft.com/services/cache/) på Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hur kan jag komma igång med Azure cache för Redis?
Det finns flera sätt att komma igång med Azure cache för Redis.

* Du kan titta på någon av våra självstudier som är tillgängliga för [.net](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.net](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node. js](cache-nodejs-get-started.md)och [python](cache-python-get-started.md).
* Du kan se [hur du skapar appar med hög prestanda med Microsoft Azure cache för Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Du kan läsa klient dokumentationen för de klienter som matchar ditt projekts utvecklings språk och se hur du använder Redis. Det finns många Redis-klienter som kan användas med Azure cache för Redis. En lista över Redis-klienter finns i [https://redis.io/clients](https://redis.io/clients).

Om du inte redan har ett Azure-konto kan du:

* [Öppna ett Azure-konto kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Du får kredit som kan användas för att prova Azure-tjänster som normalt inte är kostnadsfria. Du kan behålla kontot även efter att krediten är slut och använda kostnadsfria Azure-tjänster och -funktioner.
* [Aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Vad ska jag använda Azure cache för Redis-erbjudande och storlek?
Varje Azure cache för Redis-erbjudande ger olika nivåer av **storlek**, **bandbredd**, **hög tillgänglighet**och **SLA** -alternativ.

Tänk på följande när du väljer ett cache-erbjudande.

* **Minne**: Basic-och standard-nivåerna erbjuder 250 MB – 53 GB. Premium-nivån erbjuder upp till 1,2 TB (som ett kluster) eller 120 GB (icke-grupperad). Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).
* **Nätverks prestanda**: om du har en arbets belastning som kräver stora data flöden erbjuder Premium-nivån mer bandbredd jämfört med standard eller Basic. I varje nivå har större cache-minne också större bandbredd på grund av den underliggande virtuella datorn som är värd för cachen. Mer information finns i [följande tabell](#cache-performance).
* **Genom strömning**: Premium nivån erbjuder maximalt tillgängligt data flöde. Om cache-servern eller-klienten når bandbredds gränserna kan du få tids gränser på klient sidan. Mer information finns i följande tabell.
* **Hög tillgänglighet/SLA**: Azure cache för Redis garanterar att en standard-/Premium-cache är tillgänglig minst 99,9% av tiden. Mer information om service avtal finns i [Azure cache för Redis-priser](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Service avtalet täcker endast anslutningen till cachens slut punkter. Serviceavtalet täcker inte skydd mot dataförlust. Vi rekommenderar att du använder funktionen Redis data persistion på Premium-nivån för att öka återhämtningen mot data förlust.
* **Redis data persist**: Premium-nivån gör att du kan spara cache-data i ett Azure Storage-konto. I en Basic/standard-cache lagras alla data i minnet. Bakomliggande infrastruktur problem kan leda till potentiell data förlust. Vi rekommenderar att du använder funktionen Redis data persistion på Premium-nivån för att öka återhämtningen mot data förlust. Azure cache för Redis erbjuder RDB och AOF (kommer snart) i Redis persistence. Mer information finns i [så här konfigurerar du persistence för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).
* **Redis-kluster**: om du vill skapa cacheminnen som är större än 120 GB, eller för att Shard data över flera Redis-noder, kan du använda Redis-klustring, som är tillgängligt på Premium-nivån. Varje nod består av ett primärt/replik-cache-par för hög tillgänglighet. Mer information finns i [så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).
* **Förbättrad säkerhet och nätverks isolering**: Azure Virtual Network (VNet)-distribution ger förbättrad säkerhet och isolering för Azure-cachen för Redis, samt undernät, åtkomst kontroll principer och andra funktioner för att ytterligare begränsa åtkomst. Mer information finns i [så här konfigurerar du Virtual Network stöd för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md).
* **Konfigurera Redis**: i både standard-och Premium-nivån kan du konfigurera Redis för meddelanden om disk utrymme.
* **Maximalt antal klient anslutningar**: Premium-nivån erbjuder det maximala antalet klienter som kan ansluta till Redis, med ett högre antal anslutningar för större cacheminnen för storlek. Klustring ökar inte antalet anslutningar som är tillgängliga för en klustrad cache. Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).
* **Dedikerad kärna för redis-server**: i Premium-nivån har alla cache-storlekar en dedikerad kärna för Redis. På Basic-/standard-nivåerna har C1-storleken och över en dedikerad kärna för Redis-servern.
* **Redis är en enkel tråd** , vilket innebär att fler än två kärnor inte ger ytterligare förmån utöver att bara ha två kärnor, men större VM-storlekar har normalt större bandbredd än mindre storlek. Om cache-servern eller-klienten når bandbredds gränserna får du tids gränser på klient sidan.
* **Prestanda förbättringar**: cacheminnen på Premium-nivån distribueras på maskin vara som har snabbare processorer och ger bättre prestanda jämfört med Basic-eller standard-nivån. Cacheminnen på Premium-nivån har högre genomflöde och lägre fördröjning.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure cache för Redis-prestanda
I följande tabell visas de maximala bandbredds värden som observerats vid testning av olika storlekar på standard-och Premium-cacheminnen med `redis-benchmark.exe` från en virtuell IaaS-dator mot Azure-cachen för Redis-slutpunkt. För SSL-genomflöde används Redis-benchmark med stunnelserver för att ansluta till Azure-cachen för Redis-slutpunkten.

>[!NOTE] 
>Dessa värden är inte garanterade och det finns inget service avtal för dessa nummer, men det bör vara typiskt. Du bör läsa in testa ditt eget program för att fastställa rätt cachestorlek för ditt program.
>De här talen kan ändras när vi regelbundet publicerar nya resultat.
>

I den här tabellen kan vi Rita följande slut satser:

* Data flödet för cacheminnena som har samma storlek är högre på Premium nivån jämfört med standard nivån. Till exempel med en 6 GB cache är data flödet för P1 180 000 begär Anden per sekund (RPS) jämfört med 100 000 RPS for C3.
* Med Redis-klustring ökar data flödet linjärt när du ökar antalet Shards (noder) i klustret. Om du till exempel skapar ett P4-kluster på 10 Shards är det tillgängliga data flödet 400 000 * 10 = 4 000 000 RPS.
* Data flödet för större nyckel storlekar är högre på Premium-nivån jämfört med standard nivån.

| Prisnivå | Storlek | Processorkärnor | Tillgänglig bandbredd | värde storlek 1 KB | värde storlek 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Standardcache-storlekar** | | |**Megabit per sekund (MB/s)/megabyte per sekund (MB/s)** |**Begär Anden per sekund (RPS) icke-SSL** |**Begär Anden per sekund (RPS) SSL** |
| C0 | 250 MB | Delad | 100 / 12.5  |  15 000 |   7 500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38 000 |  20 720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41 000 |  37 000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100 000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60,000 |  55 000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102 000 |  93 000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126 000 | 120,000 |
| **Storlek på Premium-cache** | |**PROCESSOR kärnor per Shard** | **Megabit per sekund (MB/s)/megabyte per sekund (MB/s)** |**Begär Anden per sekund (RPS) som inte är SSL, per Shard** |**Begär Anden per sekund (RPS) SSL, per Shard** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172 000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350 000 | 341 000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350 000 | 341 000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373 000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373 000 |

Anvisningar om hur du konfigurerar stunnelserver eller laddar ned Redis-verktyg som `redis-benchmark.exe`finns i avsnittet [Hur kan jag köra Redis-kommandon?](#cache-commands) .

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>I vilken region ska jag hitta mitt cacheminne?
För bästa prestanda och lägsta latens, letar du upp Azure-cachen för Redis i samma region som ditt cache-klientcertifikat.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Hur faktureras jag för Azure cache för Redis?
Azure cache för Redis-priser finns [här](https://azure.microsoft.com/pricing/details/cache/). På sidan prissättning visas priser som Tim pris. Cache debiteras per minut från den tidpunkt då cacheminnet skapas tills en cache tas bort. Det finns inget alternativ för att stoppa eller pausa faktureringen av en cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Kan jag använda Azure cache för Redis med Azure Government moln, Azure Kina-moln eller Microsoft Azure Tyskland?
Ja, Azure cache för Redis finns i Azure Government Cloud, Azure Kina 21Vianet-molnet och Microsoft Azure Tyskland. URL: erna för åtkomst och hantering av Azure-cache för Redis skiljer sig åt i dessa moln jämfört med Azures offentliga moln.

| I molnet   | DNS-suffix för Redis            |
|---------|---------------------------------|
| Offentligt  | *.redis.cache.windows.net       |
| USA-förvaltad region  | *.redis.cache.usgovcloudapi.net |
| Tyskland | *.redis.cache.cloudapi.de       |
| Kina   | *.redis.cache.chinacloudapi.cn  |

Mer information om att tänka på när du använder Azure cache för Redis med andra moln finns i följande länkar.

- [Azure Government-databaser – Azure cache för Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure Kina 21Vianet-moln – Azure cache för Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)

Information om hur du använder Azure cache för Redis med PowerShell i Azure Government Cloud, Azure Kina 21Vianet-molnet och Microsoft Azure Tyskland finns i [så här ansluter du till andra moln – Azure cache för Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Vad är konfigurations alternativen för StackExchange. Redis?
StackExchange. Redis har många alternativ. I det här avsnittet beskrivs några av de vanliga inställningarna. Mer detaljerad information om StackExchange. Redis-alternativ finns i [stackexchange. Redis-konfiguration](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Beskrivning | Rekommendation |
| --- | --- | --- |
| AbortOnConnectFail |När värdet är true kommer anslutningen inte att återanslutas efter ett nätverks haveri. |Ange till false och låt StackExchange. Redis ansluta automatiskt. |
| ConnectRetry |Antalet gånger som anslutnings försöken upprepas under den första anslutningen. |Se följande information om vägledning. |
| ConnectTimeout |Timeout i MS för Connect-åtgärder. |Se följande information om vägledning. |

Vanligt vis räcker standardvärdena för-klienten. Du kan finjustera alternativen utifrån din arbets belastning.

* **Antal**
  * I ConnectRetry och ConnectTimeout är den allmänna vägledningen att sluta fungera snabbt och försöka igen. Den här vägledningen baseras på arbets belastningen och hur lång tid det tar för din klient att utfärda ett Redis-kommando och få ett svar.
  * Låt StackExchange. Redis återansluta automatiskt i stället för att kontrol lera anslutnings status och ansluta igen. **Undvik att använda egenskapen ConnectionMultiplexer. IsConnected**.
  * Snowballing – ibland kan du stöta på ett problem där du försöker igen och Återförsöken Snowball och aldrig återställer. Om Snowballing inträffar bör du överväga att använda en exponentiell backoff för nya försök enligt beskrivningen i den [allmänna vägledningen för försök](../best-practices-retry-general.md) som publicerats av Microsoft patterns & Practices Group.
  
* **Timeout-värden**
  * Överväg din arbets belastning och ange värdena efter behov. Om du lagrar stora värden anger du tids gränsen till ett högre värde.
  * Ange `AbortOnConnectFail` till falskt och låt StackExchange. Redis återansluta åt dig.
  * Använd en enda ConnectionMultiplexer-instans för programmet. Du kan använda en LazyConnection för att skapa en enda instans som returneras av en anslutnings egenskap, som du ser i [Anslut till cachen med klassen ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ange egenskapen `ConnectionMultiplexer.ClientName` till ett unikt namn för App-instansen.
  * Använd flera `ConnectionMultiplexer`-instanser för anpassade arbets belastningar.
      * Du kan följa den här modellen om du har varierande belastning i ditt program. Exempel:
      * Du kan ha en multiplexor för att hantera stora nycklar.
      * Du kan ha en multiplexor för att hantera små nycklar.
      * Du kan ange olika värden för timeout för anslutningar och omprövnings logik för varje ConnectionMultiplexer som du använder.
      * Ange `ClientName` egenskapen för varje multiplexor för att hjälpa med diagnostik.
      * Den här vägledningen kan leda till mer strömlinjeformad svars tid per `ConnectionMultiplexer`.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Vilken Azure-cache för Redis-klienter kan jag använda?
En av de fantastiska sakerna som Redis är att det finns många klienter som har stöd för många olika utvecklings språk. En aktuell lista över klienter finns i [Redis-klienter](https://redis.io/clients). För självstudier som rör flera olika språk och klienter, se [hur du använder Azure cache för Redis](cache-dotnet-how-to-use-azure-redis-cache.md) och dess artiklar på samma nivå i innehålls förteckningen.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Finns det en lokal emulator för Azure cache för Redis?
Det finns ingen lokal emulator för Azure cache för Redis, men du kan köra MSOpenTech-versionen av redis-server. exe från [kommando rads verktygen för Redis](https://github.com/MSOpenTech/redis/releases/) på den lokala datorn och ansluta till den för att få en liknande upplevelse till en lokal cache-emulator, som du ser i följande exempel:

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


Om du vill kan du konfigurera en [Redis. conf](https://redis.io/topics/config) -fil så att den stämmer bättre överens med [standardcache-inställningarna](cache-configure.md#default-redis-server-configuration) för din online Azure-cache för Redis.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hur kan jag köra Redis-kommandon?
Du kan använda något av de kommandon som anges i [Redis-kommandon](https://redis.io/commands#) förutom de kommandon som visas i Redis-kommandon som [inte stöds i Azure cache för Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Det finns flera alternativ för att köra Redis-kommandon.

* Om du har en standard-eller Premium-cache kan du köra Redis-kommandon med hjälp av [Redis-konsolen](cache-configure.md#redis-console). Redis-konsolen är ett säkert sätt att köra Redis-kommandon i Azure Portal.
* Du kan också använda kommando rads verktygen för Redis. Utför följande steg för att använda dem:
* Hämta [kommando rads verktygen för Redis](https://github.com/MSOpenTech/redis/releases/).
* Anslut till cacheminnet med hjälp av `redis-cli.exe`. Skicka i cache-slutpunkten med växeln-h och nyckeln med-a som visas i följande exempel:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Kommando rads verktygen för Redis fungerar inte med SSL-porten, men du kan använda ett verktyg som `stunnel` för att på ett säkert sätt ansluta verktygen till SSL-porten genom att följa anvisningarna i anvisningarna i [använda kommando rads verktyget Redis med Azure cache för Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) -artikeln.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Varför har inte Azure cache för Redis en MSDN-klass biblioteks referens som några av de andra Azure-tjänsterna?
Microsoft Azure Cache for Redis är baserad på populära Azure Cache for Redis med öppen källkod. Den kan nås av en mängd olika [Redis-klienter](https://redis.io/clients) för många programmeringsspråk. Varje klient har sitt eget API som gör anrop till Azure cache för Redis-instans med hjälp av [Redis-kommandon](https://redis.io/commands).

Eftersom varje klient är annorlunda finns det ingen centraliserad klass referens på MSDN, och varje klient upprätthåller sin egen referens dokumentation. Förutom referens dokumentationen finns det flera självstudier som visar hur du kommer igång med Azure cache för Redis med olika språk och cache-klienter. Information om hur du kommer åt de här självstudierna finns i [så här använder du Azure cache för Redis](cache-dotnet-how-to-use-azure-redis-cache.md) och dess artiklar på samma nivå i innehålls förteckningen.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Kan jag använda Azure cache för Redis som ett PHP-sessionsnamn?
Ja, om du vill använda Azure cache för Redis som ett PHP-sessionsnamn anger du anslutnings strängen till Azure-cachen för Redis-instansen i `session.save_path`.

> [!IMPORTANT]
> När du använder Azure cache för Redis som ett PHP-sessionsobjekt måste du URL-koda den säkerhets nyckel som används för att ansluta till cachen, som du ser i följande exempel:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Om nyckeln inte är URL-kodad kan du få ett undantag med ett meddelande som: `Failed to parse session.save_path`
>
>

Mer information om hur du använder Azure cache för Redis som en PHP-sessions-cache med PhpRedis-klienten finns i [php-session hanterare](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Vad är Redis-databaser?

Redis-databaser är bara en logisk separering av data inom samma Redis-instans. Cacheminnet delas mellan alla databaser och den faktiska minnes användningen för en specifik databas beror på de nycklar/värden som lagras i databasen. Till exempel har en C6-cache 53 GB minne och en P5 har 120 GB. Du kan välja att publicera alla 53 GB/120 GB i en databas eller dela upp den mellan flera databaser. 

> [!NOTE]
> När du använder en Premium Azure-cache för Redis med klustring aktive rad är endast databas 0 tillgängligt. Den här begränsningen är en inbyggd Redis-begränsning och är inte särskilt för Azure cache för Redis. Mer information finns i [behöver jag göra ändringar i klient programmet för att använda kluster?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>När ska jag aktivera icke-SSL-porten för anslutning till Redis?
Redis-servern stöder inte SSL, men Azure cache för Redis gör det. Om du ansluter till Azure cache för Redis och klienten stöder SSL, t. ex. StackExchange. Redis, bör du använda SSL.

>[!NOTE]
>Icke-SSL-porten är inaktive rad som standard för nya Azure cache för Redis-instanser. Om klienten inte har stöd för SSL måste du aktivera icke-SSL-porten genom att följa anvisningarna i avsnittet [åtkomst portar](cache-configure.md#access-ports) i artikeln [Konfigurera en cache i Azure cache för Redis](cache-configure.md) .
>
>

Redis-verktyg som `redis-cli` fungerar inte med SSL-porten, men du kan använda ett verktyg som `stunnel` för att på ett säkert sätt ansluta verktygen till SSL-porten genom att följa anvisningarna i avsnittet [presentera ASP.net för Redis för för hands version](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

Instruktioner för hur du hämtar Redis-verktygen finns i avsnittet [Hur kan jag köra Redis-kommandon?](#cache-commands) .

### <a name="what-are-some-production-best-practices"></a>Vad är några produktions metod tips?
* [Metod tips för StackExchange. Redis](#stackexchangeredis-best-practices)
* [Konfiguration och begrepp](#configuration-and-concepts)
* [Prestandatestning](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Metod tips för StackExchange. Redis
* Ange `AbortConnect` till falskt och låt sedan ConnectionMultiplexer återansluta automatiskt. [Mer information finns här](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Återanvänd ConnectionMultiplexer – skapa inte någon ny för varje begäran. `Lazy<ConnectionMultiplexer>` mönstret som [visas här](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) rekommenderas.
* Redis fungerar bäst med mindre värden, så Överväg att chopping upp större data i flera nycklar. I [den här Redis-diskussionen](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)betraktas 100 kB som stor. I [den här artikeln](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) finns ett exempel på problem som kan orsakas av stora värden.
* Konfigurera dina [Inställningar för trådpool](#important-details-about-threadpool-growth) för att undvika tids gränser.
* Använd minst standard connectTimeout på 5 sekunder. Intervallet ger StackExchange. Redis tillräckligt med tid för att återupprätta anslutningen i händelse av ett nätverks blip.
* Tänk på de prestanda kostnader som är kopplade till olika åtgärder som du kör. Till exempel är `KEYS` kommandot en O (n)-åtgärd och bör undvikas. [Redis.io-webbplatsen](https://redis.io/commands/) innehåller information kring tids komplexiteten för varje åtgärd som stöds. Klicka på varje kommando för att se komplexiteten för varje åtgärd.

#### <a name="configuration-and-concepts"></a>Konfiguration och begrepp
* Använd standard-eller Premium nivån för produktions system. Basic-nivån är ett enda Node-system utan datareplikering och inget service avtal. Använd också minst en C1-cache. C0-cache används vanligt vis för enkla utvecklings-och test scenarier.
* Kom ihåg att Redis är ett **minnes intern** data lager. Läs [den här artikeln](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) så att du är medveten om scenarier där data förlust kan uppstå.
* Utveckla ditt system så att det kan hantera anslutnings signaler [på grund av korrigeringar och redundans](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Prestandatestning
* Börja med att använda `redis-benchmark.exe` för att få en känsla för eventuella data flöden innan du skriver dina egna perf-tester. Eftersom `redis-benchmark` inte stöder SSL, måste du [Aktivera icke-SSL-porten via Azure Portal](cache-configure.md#access-ports) innan du kör testet. Exempel finns i [Hur kan jag mäta och testa prestanda för mitt cacheminne?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Den virtuella klient datorn som används för testning ska finnas i samma region som Azure-cachen för Redis-instansen.
* Vi rekommenderar att du använder Dv2 VM-serien för din klient eftersom de har bättre maskin vara och ger bästa möjliga resultat.
* Kontrol lera att den virtuella klient datorn du väljer har minst lika mycket data behandlings-och bandbredds kapacitet som det cacheminne som du testar.
* Aktivera VRSS på klient datorn om du använder Windows. [Mer information finns här](https://technet.microsoft.com/library/dn383582.aspx).
* Redis-instanser på Premium-nivå har bättre nätverks svars tid och data flöde eftersom de körs på bättre maskin vara för både CPU och nätverk.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Vad är några av överväganden när du använder vanliga Redis-kommandon?

* Undvik att använda vissa redis-kommandon som tar lång tid att slutföra, om du inte helt förstår effekten av dessa kommandon. Kör till exempel inte kommandot [nycklar](https://redis.io/commands/keys) i produktion. Beroende på antalet nycklar kan det ta lång tid att returnera. Redis är en enkel tråds Server och bearbetar kommandon en i taget. Om du har andra kommandon som utfärdas efter nycklar bearbetas de inte förrän Redis bearbetar kommandot nycklar. [Redis.io-webbplatsen](https://redis.io/commands/) innehåller information kring tids komplexiteten för varje åtgärd som stöds. Klicka på varje kommando för att se komplexiteten för varje åtgärd.
* Nyckel storlekar – ska jag använda små nycklar/värden eller stora nycklar/värden? Det beror på scenariot. Om ditt scenario kräver större nycklar kan du justera ConnectionTimeout och sedan försöka igen och justera din logik för återförsök. Från ett redis server-perspektiv ger mindre värden bättre prestanda.
* Dessa överväganden innebär inte att du inte kan lagra större värden i Redis; Du måste vara medveten om följande överväganden. Fördröjningen är högre. Om du har en uppsättning data som är större och en som är mindre, kan du använda flera ConnectionMultiplexer-instanser, var och en som kon figurer ATS med en annan uppsättning tids gräns och nya försök, enligt beskrivningen i föregående [vad gör avsnittet stackexchange. Redis konfigurations alternativ](#cache-configuration) .

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hur kan jag mäta och testa prestanda för mitt cacheminne?
* [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet. Du kan visa värdena på Azure-portalen eller [hämta och granska](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem med valfritt verktyg.
* Du kan använda Redis-benchmark. exe för att läsa in test av Redis-servern.
* Se till att belastnings test klienten och Azure-cachen för Redis finns i samma region.
* Använd Redis-cli. exe och övervaka cachen med hjälp av kommandot INFO.
* Om din belastning orsakar hög fragmentering i minnet, bör du skala upp till en större cachestorlek.
* Instruktioner för hur du hämtar Redis-verktygen finns i avsnittet [Hur kan jag köra Redis-kommandon?](#cache-commands) .

Följande kommandon innehåller ett exempel på hur du använder Redis-benchmark. exe. Kör dessa kommandon från en virtuell dator i samma region som ditt cacheminne för korrekta resultat.

* Testa Pipeliniska UPPSÄTTNINGs begär Anden med en last i 1 KB

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testa pipeline GET-begäranden med en nytto last på 1 KB.
  Obs! kör SET-testet som visas ovan först för att fylla i cachen

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Viktig information om hur du växer i trådpool
CLR-trådpool har två typer av trådar – "Work" och "I/O-sluten port" (IOCP) trådar.

* Arbets trådar används för saker som att bearbeta `Task.Run(…)`eller `ThreadPool.QueueUserWorkItem(…)` metoder. Dessa trådar används också av olika komponenter i CLR när arbetet måste inträffa i en bakgrunds tråd.
* IOCP-trådar används när asynkront IO sker, till exempel vid läsning från nätverket.

Trådpoolen ger nya arbets trådar eller I/O-slutförande-trådar på begäran (utan begränsning) tills den når inställningen "minimum" för varje typ av tråd. Som standard anges det minsta antalet trådar som är antalet processorer i ett system.

När antalet befintliga (upptagna) trådar träffar "minsta antal trådar", kommer trådpoolen att reglera den hastighet med vilken den infogar nya trådar till en tråd per 500 millisekunder. Normalt, om ditt system får en burst av arbete som behöver en IOCP-tråd, kommer den att bearbeta arbetet snabbt. Men om arbets belastningen är mer än den konfigurerade "lägsta"-inställningen kommer det att finnas en fördröjning vid bearbetningen av en del av arbetet eftersom trådpoolen väntar på att ett av två saker sker.

1. En befintlig tråd blir fri att bearbeta arbetet.
2. Ingen befintlig tråd blir kostnads fri för 500 ms, så en ny tråd skapas.

I princip innebär det att när antalet upptagna trådar är större än minsta antal trådar betalar du troligen en 500-ms-fördröjning innan nätverks trafiken bearbetas av programmet. Det är också viktigt att Observera att när en befintlig tråd är inaktiv i mer än 15 sekunder (baserat på vad jag minns) kommer den att rensas och den här cykeln för tillväxt och krympning kan upprepas.

Om vi tittar på ett exempel fel meddelande från StackExchange. Redis (build 1.0.450 eller senare), kommer du att se att den nu skriver ut trådpool-statistik (se IOCP och WORKer-information nedan).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

I det tidigare exemplet kan du se att för IOCP-tråden det finns sex upptagna trådar och att systemet är konfigurerat att tillåta fyra minsta trådar. I det här fallet skulle klienten troligt vis sett 2 500-ms fördröjningar, eftersom 6 > 4.

Observera att StackExchange. Redis kan trycka på timeout om tillväxten för antingen IOCP eller arbets trådar blir begränsad.

### <a name="recommendation"></a>Rekommendation

Med hänsyn till den här informationen rekommenderar vi starkt att kunderna anger det minsta konfiguration svärdet för IOCP och arbets trådar till något större än standardvärdet. Vi kan inte ge en storleks topp – all vägledning om vad det här värdet ska bero på att rätt värde för ett program sannolikt är för högt eller lågt för ett annat program. Den här inställningen kan också påverka prestandan för andra delar av komplexa program, så att varje kund måste finjustera den här inställningen efter deras specifika behov. En bra start punkt är 200 eller 300 och sedan testa och justera efter behov.

Så här konfigurerar du den här inställningen:

* Vi rekommenderar att du ändrar den här inställningen program mässigt genom att använda filen [trådpool. SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) i `global.asax.cs`. Exempel:

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
  > Värdet som anges av den här metoden är en global inställning som påverkar hela AppDomain. Om du till exempel har en dator med 4 kärnor och vill ställa in *minWorkerThreads* och *minIoThreads* på 50 per processor under körningen använder du **trådpool. SetMinThreads (200, 200)** .

* Du kan också ange minsta antal trådar genom att använda [konfigurations inställningen *MinIoThreads* eller *minWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) under konfigurations elementet `<processModel>` i `Machine.config`, som vanligt vis finns på `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`. **Att ställa in antalet minsta trådar på det här sättet rekommenderas vanligt vis inte, eftersom det är en systemtäckande inställning.**

  > [!NOTE]
  > Värdet som anges i det här konfigurations elementet är en inställning *per kärna* . Om du till exempel har en 4 core-dator och vill att *minIoThreads* -inställningen ska vara 200 vid körning använder du `<processModel minIoThreads="50"/>`.
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Aktivera server-GC för att få mer data flöde på klienten när du använder StackExchange. Redis
Att aktivera server GC kan optimera klienten och tillhandahålla bättre prestanda och data flöde när du använder StackExchange. Redis. Mer information om Server GC och hur du aktiverar den finns i följande artiklar:

* [Så här aktiverar du Server GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Grunderna i skräp insamling](/dotnet/standard/garbage-collection/fundamentals)
* [Skräp insamling och prestanda](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Prestanda överväganden kring anslutningar

Varje pris nivå har olika gränser för klient anslutningar, minne och bandbredd. Varje cache-storlek tillåter *upp till* ett visst antal anslutningar, och varje anslutning till Redis har kopplad till sig. Ett exempel på sådana kostnader är CPU och minnes användning som ett resultat av TLS/SSL-kryptering. Den maximala anslutnings gränsen för en specifik cachestorlek förutsätter en lätt inläst cache. Om belastningen från anslutnings belastning *plus* belastning från klient åtgärder överskrider kapaciteten för systemet, kan cachen uppleva kapacitets problem även om du inte har överskridit anslutnings gränsen för den aktuella cachestorleken.

Mer information om olika anslutnings begränsningar för varje nivå finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/). Mer information om anslutningar och andra standardkonfigurationer finns i [standard konfigurationen för Redis-servern](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hur gör jag för att övervaka hälso tillstånd och prestanda för mitt cacheminne?
Microsoft Azure cache för Redis-instanser kan övervakas i [Azure Portal](https://portal.azure.com). Du kan visa mått, fästa mått diagram på Start sidan, anpassa datum-och tidsintervallet för övervaknings diagram, lägga till och ta bort mått från diagrammen och ange aviseringar när vissa villkor uppfylls. Mer information finns i [övervaka Azure cache för Redis](cache-how-to-monitor.md).

**Resurs menyn** i Azure cache för Redis innehåller också flera verktyg för att övervaka och felsöka dina cacheminnen.

* **Diagnostisera och lösa problem** innehåller information om vanliga problem och strategier för att lösa dem.
* **Resource Health** bevakar din resurs och meddelar dig om den körs som förväntat. Mer information om Azure Resource Health Service finns i [Översikt över Azure Resource Health](../resource-health/resource-health-overview.md).
* Den **nya support förfrågan** innehåller alternativ för att öppna en supportbegäran för din cache.

Med de här verktygen kan du övervaka Azure-cachens hälso tillstånd för Redis-instanser och hjälpa dig att hantera dina cachelagring-program. Mer information finns i avsnittet "support & fel söknings inställningar" i [så här konfigurerar du Azure cache för Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Varför ser jag timeout?
Timeout sker i klienten som du använder för att prata med Redis. När ett kommando skickas till Redis-servern, köas kommandot och Redis-servern hämtar sedan kommandot och kör det. Klienten kan dock ta lång tid under den här processen och om den inträffar utlöses ett undantags fel på den anropande sidan. Mer information om hur du felsöker timeout-problem finns i [fel sökning på klient sidan](cache-troubleshoot-client.md) och [stackexchange. Redis timeout-undantag](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Varför var min klient frånkopplad från cachen?
Följande är några vanliga orsaker till att en cache kopplas från.

* Orsaker på klient Sidan
  * Klient programmet distribuerades om.
  * Klient programmet utförde en skalnings åtgärd.
    * Om Cloud Services eller Web Apps kan det bero på automatisk skalning.
  * Nätverks lagret på klient sidan har ändrats.
  * Tillfälliga fel uppstod i klienten eller på nätverks noderna mellan klienten och servern.
  * Gränserna för bandbredds tröskeln har uppnåtts.
  * PROCESSOR bindnings åtgärder tog för lång tid att slutföra.
* Orsaker på Server Sidan
  * I standard-cachen initierade Azure cache för Redis-tjänsten en redundans från den primära noden till den sekundära noden.
  * Azure har korrigerat den instans där cachen distribuerades
    * Detta kan vara för redis-server uppdateringar eller underhåll av den virtuella datorn.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Vilket Azure Cache-erbjudande är bäst för mig?
> [!IMPORTANT]
> Enligt föregående års [meddelande](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)har Azure Managed Cache service och Azure cachelagring i rollinstanser-tjänsten **dragits tillbaka** den 30 november 2016. Vi rekommenderar att du använder [Azure cache för Redis](https://azure.microsoft.com/services/cache/). Information om migrering finns i [Migrera från Managed Cache service till Azure cache för Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure cache för Redis är allmänt tillgängligt i storlekar upp till 120 GB och har ett tillgänglighets-SLA på 99,9%. Den nya [Premium-nivån](cache-premium-tier-intro.md) erbjuder en storlek på upp till 1,2 TB och stöd för klustring, VNet och persistence, med ett service avtal på 99,9%.

Azure cache för Redis ger kunderna möjlighet att använda en säker, dedikerad Azure-cache för Redis, som hanteras av Microsoft. Med det här erbjudandet kan du utnyttja den omfattande funktions uppsättningen och det eko system som tillhandahålls av Redis, samt tillförlitlig värd och övervakning från Microsoft.

Till skillnad från traditionella cacheminnen som bara hanterar nyckel/värde-par, är Redis populärt för data typerna med hög prestanda. Redis har också stöd för att köra atomiska åtgärder på dessa typer, t. ex. att lägga till en sträng. ökar värdet i en hash; Skicka till en lista; beräknar uppsättning snitt, Union och skillnad; eller hämta medlemmen med den högsta rangordningen i en sorterad uppsättning. Andra funktioner är stöd för transaktioner, pub/sub, Lua-skript, nycklar med begränsad tid till Live och konfigurations inställningar som gör att Redis fungerar mer som en traditionell cache.

En annan viktig aspekt till Redis lyckades är det felfria, livfulla eko systemet med öppen källkod som skapats runt det. Detta återspeglas i den olika uppsättningen Redis-klienter som är tillgängliga på flera språk. Det här eko systemet och en mängd olika klienter gör att Azure cache för Redis kan användas av nästan alla arbets belastningar som du skulle bygga i Azure.

Mer information om att komma igång med Azure cache för Redis finns i [så här använder du Azure cache för Redis](cache-dotnet-how-to-use-azure-redis-cache.md) och [Azure cache för Redis-dokumentation](index.md).

### <a name="managed-cache-service"></a>Hanterad cache service
[Managed Cache service drogs tillbaka 30 november 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Om du vill visa Arkiverad dokumentation, se [arkiverad Managed Cache service dokumentation](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Cachelagring i rollinstanser
[Cachelagring i rollinstanser drogs tillbaka 30 november 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Om du vill visa Arkiverad dokumentation, se [arkiverad cachelagring i rollinstanser dokumentation](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
