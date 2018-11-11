---
title: Azure Redis Cache vanliga frågor och svar | Microsoft Docs
description: Läs svaren på vanliga frågor, mönster och metodtips för Azure Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: wesmc
ms.openlocfilehash: 21b5050996428328bfda314b2f2242ed2a766e74
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239771"
---
# <a name="azure-redis-cache-faq"></a>Vanliga frågor och svar för Azure Redis Cache
Lär dig svar på vanliga frågor, mönster och metodtips för Azure Redis Cache.

## <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?
Om din fråga inte visas kan för oss berätta och vi hjälper dig att hitta något svar.

* Du kan ställa en fråga i kommentarerna i slutet av den här vanliga frågor och svar och interagera med Azure Cache-teamet och andra community-medlemmar om den här artikeln.
* För att nå en bredare publik, kan du ställa en fråga på den [MSDN-Forum för Azure Cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) och interagera med Azure Cache-teamet och andra medlemmar i communityn.
* Om du vill skicka en funktionsförfrågan kan du skicka dina förfrågningar och idéer om [Azure Redis Cache User Voice](https://feedback.azure.com/forums/169382-cache).
* Du kan också skicka ett e-postmeddelande till oss på [Azure Cache externa Feedback](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Azure Redis Cache-grunder
Frågor och svar i det här avsnittet beskriver några av grunderna i Azure Redis Cache.

* [Vad är Azure Redis Cache?](#what-is-azure-redis-cache)
* [Hur kan jag igång med Azure Redis Cache?](#how-can-i-get-started-with-azure-redis-cache)

Följande vanliga frågor och svar täcker grundläggande begrepp och frågor om Azure Redis Cache och besvaras i de andra vanliga frågor och svar-avsnitt.

* [Vilket erbjudande och vilken storlek ska jag använda för Redis-cache?](#what-redis-cache-offering-and-size-should-i-use)
* [Vilka Redis cache-klienter kan jag använda?](#what-redis-cache-clients-can-i-use)
* [Finns det en lokal emulator för Azure Redis Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Hur övervakar jag hälsotillstånd och prestanda för mitt cacheminne?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Planera vanliga frågor och svar
* [Vilket erbjudande och vilken storlek ska jag använda för Redis-cache?](#what-redis-cache-offering-and-size-should-i-use)
* [Azure Redis Cache-prestanda](#azure-redis-cache-performance)
* [I vilken region bör jag hitta mitt cacheminne?](#in-what-region-should-i-locate-my-cache)
* [Hur debiteras jag för Azure Redis Cache?](#how-am-i-billed-for-azure-redis-cache)
* [Kan jag använda Azure Redis Cache med Azure Government-molnet, Azure Kina-molnet eller Microsoft Azure Tyskland?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Utveckling av vanliga frågor och svar
* [Vad gör konfigurationsalternativ för StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Vilka Redis cache-klienter kan jag använda?](#what-redis-cache-clients-can-i-use)
* [Finns det en lokal emulator för Azure Redis Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Hur kan jag köra Redis-kommandon?](#how-can-i-run-redis-commands)
* [Varför har inte en biblioteksreferens för MSDN-klass som några av de andra Azure-tjänsterna i Azure Redis Cache?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Kan jag använda Azure Redis Cache som en PHP-sessionscache?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [Vad är Redis-databaser?](#what-are-redis-databases)

## <a name="security-faqs"></a>Vanliga frågor om Security
* [När ska jag aktivera porten utan SSL för att ansluta till Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Produktion vanliga frågor och svar
* [Vilka är några Metodtips för produktion?](#what-are-some-production-best-practices)
* [Vilka är några av överväganden när du använder vanliga Redis-kommandon?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hur kan jag göra benchmark-test och testa prestanda hos mitt cacheminne?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Viktig information om arbetstråd tillväxt](#important-details-about-threadpool-growth)
* [Aktivera server GC att få större dataflöde på klienten när du använder StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Prestandaöverväganden runt anslutningar](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Övervakning och felsökning av vanliga frågor och svar
Frågor och svar i det här avsnittet beskriver vanliga övervakning och felsökning frågor. Läs mer om att övervaka och felsöka Azure Redis Cache-instanser, [så här övervakar du Azure Redis Cache](cache-how-to-monitor.md) och [så här felsöker du Azure Redis Cache](cache-how-to-troubleshoot.md).

* [Hur övervakar jag hälsotillstånd och prestanda för mitt cacheminne?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Varför ser jag tidsgränser?](#why-am-i-seeing-timeouts)
* [Varför kopplades från en kunds från cachen?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Vanliga frågor och svar tidigare Cache-erbjudande
* [Vilket Azure Cache-erbjudande är bäst för mig?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>Vad är Azure Redis Cache?
Azure Redis Cache baseras på den populära [Redis-cachen](http://redis.io) med öppen källkod. Den ger dig tillgång till en säker, dedikerad Redis-cache som hanteras av Microsoft och kan nås från alla program i Azure. En mer detaljerad översikt finns i den [Azure Redis Cache](https://azure.microsoft.com/services/cache/) produktsidan på Azure.com.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Hur kan jag igång med Azure Redis Cache?
Det finns flera sätt du kan komma igång med Azure Redis Cache.

* Du kan titta på en av våra självstudier som är tillgängliga för [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), och [Python](cache-python-get-started.md).
* Du kan titta på [hur du bygger högpresterande appar med Microsoft Azure Redis Cache](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Du kan checka ut i dokumentationen för klienter för klienter som matchar ditt projekt för att se hur du använder Redis programmeringsspråk. Det finns många Redis-klienter som kan användas med Azure Redis Cache. En lista över Redis-klienter finns i [ http://redis.io/clients ](http://redis.io/clients).

Om du inte redan har ett Azure-konto, kan du:

* [Öppna ett Azure-konto kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Du får kredit som kan användas för att prova Azure-tjänster som normalt inte är kostnadsfria. Du kan behålla kontot även efter att krediten är slut och använda kostnadsfria Azure-tjänster och -funktioner.
* [Aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Vilket erbjudande och vilken storlek ska jag använda för Redis-cache?
Varje Azure Redis Cache-erbjudande innehåller olika nivåer av **storlek**, **bandbredd**, **hög tillgänglighet**, och **SLA** alternativ.

Här följer överväganden för att välja en Cache-erbjudande.

* **Minne**: nivåerna Basic och Standard har 250 MB – 53 GB. Premium-nivån erbjuder upp till 530 GB. Mer information finns i [priser för Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).
* **Nätverksprestanda**: Om du har en arbetsbelastning som kräver hög genomströmning på Premium-nivån ger högre bandbredd jämfört med Standard- eller Basic. Inom varje nivå har större storlek cacheminnen också mer bandbredd på grund av den underliggande virtuella datorn som är värd för cachen. Se den [följande tabell](#cache-performance) för mer information.
* **Dataflöde**: The Premium-nivån erbjuder maximal tillgängliga genomströmning. Om cache-server eller klient når bandbreddsgränserna, får du timeout-fel på klientsidan. Mer information finns i följande tabell.
* **Hög tillgänglighet/SLA**: Azure Redis Cache garanterar att en Standard/Premium-cache är tillgänglig minst 99,9% av tiden. Mer information om serviceavtal finns [priser för Azure Redis Cache](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). SERVICEAVTALET täcker endast anslutningen mellan cachens slutpunkter. Serviceavtalet täcker inte skydd mot dataförlust. Vi rekommenderar att du använder Redis-datapersistensfunktionen på Premium-nivån för att öka skyddet mot dataförlust.
* **Redis-Datapersistens**: The Premium-nivån kan du spara Cachedata i ett Azure Storage-konto. Alla data lagras i en Basic-och Standard-cache, endast i minnet. Om det finns kan underliggande infrastruktur problem det vara potentiell dataförlust. Vi rekommenderar att du använder Redis-datapersistensfunktionen på Premium-nivån för att öka skyddet mot dataförlust. Azure Redis Cache erbjuder RDB och AOF (kommer snart) alternativ i Redis persistence. Mer information finns i [så här konfigurerar du persistence för Premium Azure Redis Cache](cache-how-to-premium-persistence.md).
* **Redis-kluster**: att skapa cacheminnen större än 53 GB och Fragmentera data över flera Redis-noder, du kan använda Redis-klustring, som är tillgängligt på Premium-nivån. Varje nod består av en primär/replik cache-par för hög tillgänglighet. Mer information finns i [Konfigurera klustring för premium Azure Redis-cache](cache-how-to-premium-clustering.md).
* **Förbättrad säkerhet och nätverk isolering**: Azure Virtual Network (VNET)-distributionen tillhandahåller förbättrad säkerhet och isolering för din Azure Redis Cache, samt undernät, åtkomstkontrollprinciper och andra funktioner för att ytterligare begränsa åtkomst. Mer information finns i [Konfigurera Virtual Network-stöd för premium Azure Redis-cache](cache-how-to-premium-vnet.md).
* **Konfigurera Redis**: nivåerna Standard och Premium, kan du konfigurera Redis för Keyspace-meddelanden.
* **Högsta antal klientanslutningar**: The Premium-nivån erbjuder det maximala antalet klienter som kan ansluta till Redis med ett högre antal anslutningar för större storlekar cacheminnen. Klustring ökar inte antalet anslutningar som är tillgängliga för en klustrad cache. Mer information finns i [priser för Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).
* **Dedikerade kärnor för Redis Server**: Premium-nivån alla cache-storlekar har en dedikerad kärna för Redis. På Basic-och Standard-nivåerna och C1 storlek och ovan har en dedikerad kärna för Redis-servern.
* **Redis är single-threaded** så att ha fler än två kärnor inte ge extra förmån över har bara två kärnor, men större storlekar för Virtuella datorer har vanligtvis mer bandbredd än mindre. Om cache-server eller klient når bandbreddsgränserna kan få du timeout-fel på klientsidan.
* **Prestandaförbättringar**: cacheminnen på Premium-nivån har distribuerats på maskinvara som har snabbare processorer, vilket ger bättre prestanda jämfört med Basic eller Standard-nivån. Premium-nivån cacheminnen har högre dataflöde och kortare svarstider.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Azure Redis Cache-prestanda
I följande tabell visas de värden för maximal bandbredd som observerats vid testning av olika storlekar av Standard och Premium cachelagrar med hjälp av `redis-benchmark.exe` från en Iaas-VM mot Azure Redis Cache-slutpunkten. För SSL-dataflöde används redis-benchmark med stunnel för att ansluta till Azure Redis Cache-slutpunkten.

>[!NOTE] 
>Dessa värden garanteras inte och det finns inget serviceavtal för dessa siffror, men bör vara vanliga. Du bör läsa in testa ditt eget program för att fastställa rätt cachestorleken för ditt program.
>Dessa siffror kan ändras när vi publicerar nya resultaten med jämna mellanrum.
>

Vi kan rita följande slutsatser från den här tabellen:

* Dataflöde för cacheminnen som har samma storlek är högre på Premium-nivån jämfört med Standard-nivån. Till exempel med 6 GB Cache är dataflödet som P1 180 000 RPS jämfört med 100 000 för C3.
* Med Redis-klustring, ökar dataflödet linjärt när du ökar antalet shards (noder) i klustret. Till exempel om du skapar ett P4-kluster med 10 shards kan det tillgängliga genomflödet är 400 000 * 10 = 4 miljoner RPS.
* Dataflöde för större nyckelstorlekar är högre på Premium-nivån jämfört med Standard-nivån.

| Prisnivå | Storlek | Processorkärnor | Tillgänglig bandbredd | 1 KB storlek | 1 KB storlek |
| --- | --- | --- | --- | --- | --- |
| **Storlekar för standardcache** | | |**Megabit per sekund (Mbit/s) / megabyte per sekund (MBIT/s)** |**Begäranden per sekund (RPS) icke-SSL** |**Begäranden per sekund (RPS) SSL** |
| C0 |250 MB |Delad |100 / 12.5 |15 000 |7 500 |
| C1 |1 GB |1 |500 / 62.5 |38,000 |20,720 |
| C2 |2,5 GB |2 |500 / 62.5 |41,000 |37,000 |
| C3 |6 GB |4 |1000 / 125 |100 000 |90,000 |
| C4 |13 GB |2 |500 / 62.5 |60,000 |55,000 |
| C5 |26 GB |4 |1,000 / 125 |102 000 kr |93,000 |
| C6 |53 GB |8 |2,000 / 250 |126,000 |120,000 |
| **Storlekar för Premium-cache** | |**CPU-kärnor per shard** | **Megabit per sekund (Mbit/s) / megabyte per sekund (MBIT/s)** |**Begäranden per sekund (RPS) icke-SSL, per shard** |**Begäranden per sekund (RPS) SSL, per shard** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13 GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26 GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53 GB |8 |6,000 / 750 |400,000 |373,000 |

Mer information om hur du konfigurerar stunnel eller hämta Redis-verktyg som `redis-benchmark.exe`, finns i den [hur kan jag köra Redis-kommandon?](#cache-commands) avsnittet.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>I vilken region bör jag hitta mitt cacheminne?
För bästa prestanda och lägsta svarstid, letar du upp din Azure Redis Cache i samma region som din cacheklientprogrammet.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>Hur debiteras jag för Azure Redis Cache?
Priser för Azure Redis Cache är [här](https://azure.microsoft.com/pricing/details/cache/). Sidan med priser visas priser som ett timpris. Cacheminnen faktureras per-minut från den tidpunkt då cachen har skapats förrän den tid som ett cacheminne har tagits bort. Det finns inget alternativ för stoppas eller pausas faktureringen för cache.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Kan jag använda Azure Redis Cache med Azure Government-molnet, Azure Kina-molnet eller Microsoft Azure Tyskland?
Ja, Azure Redis Cache är tillgängligt i Azure Government-molnet, Azure Kina-molnet och Microsoft Azure Tyskland. URL: er för att komma åt och hantera Azure Redis Cache är olika i dessa moln jämfört med Azures offentliga moln. 

| Molnet   | DNS-suffixet för Redis            |
|---------|---------------------------------|
| Offentligt  | *.redis.cache.windows.net       |
| USA-förvaltad region  | *.redis.cache.usgovcloudapi.net |
| Tyskland | *.redis.cache.cloudapi.de       |
| Kina   | *.redis.cache.chinacloudapi.cn  |

Se följande länkar för mer information om överväganden när du använder Azure Redis Cache med andra moln.

- [Azure Government-databaser – Azure Redis Cache](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Azure Kina-molnet – Azure Redis Cache](https://www.azure.cn/documentation/services/redis-cache/)
- [Microsoft Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)

Information om hur du använder Azure Redis Cache med PowerShell i Azure Government-molnet, Azure Kina-molnet och Microsoft Azure Germany finns i [hur du ansluter till andra moln – Azure Redis Cache PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Vad gör konfigurationsalternativ för StackExchange.Redis?
StackExchange.Redis har många alternativ. Det här avsnittet innehåller information om några av de vanliga inställningarna. Mer detaljerad information om alternativ för StackExchange.Redis finns i [StackExchange.Redis configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Beskrivning | Rekommendation |
| --- | --- | --- |
| AbortOnConnectFail |När värdet är true, anslutningen kommer inte återansluta efter ett nätverksfel. |Inställt på FALSKT och låt StackExchange.Redis återansluta automatiskt. |
| ConnectRetry |Hur många gånger Upprepa anslutningsförsök under inledande ansluta. |Se följande information för vägledning. |
| ConnectTimeout |Tidsgräns i ms för anslutningsåtgärder. |Se följande information för vägledning. |

Vanligtvis är standardvärdena för klienten tillräckliga. Du kan finjustera alternativ baserat på din arbetsbelastning.

* **Återförsök**
  * För ConnectRetry och ConnectTimeout är allmänna riktlinjer att misslyckas snabbt och försök igen. Den här vägledningen baseras på din arbetsbelastning och hur lång tid på genomsnittlig det tar för klienten att utfärda ett Redis-kommando och ta emot ett svar.
  * Låt StackExchange.Redis återansluta automatiskt i stället för att kontrollera anslutningsstatus och återansluter själv. **Undvik att använda egenskapen ConnectionMultiplexer.IsConnected**.
  * Snowballing - ibland kan du stöta på ett problem där du försöker igen och nya försök snowball och aldrig återställs. Om snowballing inträffar bör du använda en exponentiell backoff återförsök algoritm enligt beskrivningen i [allmänna riktlinjer för återförsök](../best-practices-retry-general.md) har publicerats av Microsoft Patterns & Practices-gruppen.
* **Timeout-värden**
  * Överväg att din arbetsbelastning och ange värden i enlighet med detta. Om du lagrar stora värden, kan du ange tidsgränsen till ett högre värde.
  * Ange `AbortOnConnectFail` till false och låt StackExchange.Redis återansluta åt dig.
  * Använda en enda ConnectionMultiplexer-instans för programmet. Du kan använda en LazyConnection för att skapa en instans som returneras av en Anslutningsegenskapen enligt [ansluta till cacheminnet med hjälp av klassen ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ange den `ConnectionMultiplexer.ClientName` egenskap till en instans unika appnamn i diagnostiskt syfte.
  * Använda flera `ConnectionMultiplexer` instanser för anpassade arbetsbelastningar.
      * Om du har varierande belastning i ditt program kan du följa den här modellen. Exempel:
      * Du kan ha en multiplexor för att hantera stora nycklar.
      * Du kan ha en multiplexor för att hantera små nycklar.
      * Du kan ange olika värden för timeout och logik för omprövning för varje ConnectionMultiplexer som du använder.
      * Ange den `ClientName` egenskapen på varje multiplexor som underlättar diagnostik.
      * Den här vägledningen kan leda till mer strömlinjeformad svarstider per `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Vilka Redis cache-klienter kan jag använda?
En av fördelarna med Redis är att det finns många klienter som stöder många olika programmeringsspråk. En aktuell lista över klienter finns i [Redis-klienter](http://redis.io/clients). Självstudier som omfattar flera olika språk och klienter finns i [hur du använder Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) och klicka på önskat språk från växlaren språk överst i artikeln.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Finns det en lokal emulator för Azure Redis Cache?
Det finns ingen lokal emulator för Azure Redis Cache, men du kan köra den MSOpenTech versionen av redis-server.exe från den [Redis kommandoradsverktyg](https://github.com/MSOpenTech/redis/releases/) på lokalt datorn och ansluta till den att få samma möjligheter till en lokal cache-emulator som du ser i följande exempel:

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


Du kan också konfigurera en [redis.conf](http://redis.io/topics/config) filen för att bättre matcha den [standardinställningar för cache](cache-configure.md#default-redis-server-configuration) för online Azure Redis-cachen om du vill.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hur kan jag köra Redis-kommandon?
Du kan använda någon av de kommandon som finns på [Redis-kommandon](http://redis.io/commands#) förutom de kommandon som finns på [Redis-kommandon som inte stöds i Azure Redis Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Du har flera alternativ för att köra Redis-kommandon.

* Om du har en Standard- eller Premium-cache kan du köra Redis-kommandon med hjälp av den [Redis-konsolen](cache-configure.md#redis-console). Redis-konsolen innehåller ett säkert sätt att köra Redis-kommandon i Azure-portalen.
* Du kan också använda Redis-kommandoradsverktygen. Utför följande steg för att använda dem:
* Ladda ned den [Redis kommandoradsverktyg](https://github.com/MSOpenTech/redis/releases/).
* Ansluta till en cache med hjälp av `redis-cli.exe`. Skicka in den cache-slutpunkten med den -h växla och nyckeln med hjälp av – en som visas i följande exempel:
* `redis-cli -h <redis cache name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Redis-kommandoradsverktyg fungerar inte med SSL-porten, men du kan använda ett verktyg som `stunnel` att på ett säkert sätt ansluta verktygen till SSL-porten genom att följa anvisningarna i den [Vi presenterar ASP.NET-Sessionstillståndsprovider för Redis-förhandsversion Versionen](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogginlägg.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Varför har inte en biblioteksreferens för MSDN-klass som några av de andra Azure-tjänsterna i Azure Redis Cache?
Microsoft Azure Redis Cache är baserad på den populära öppna källkoden Redis Cache och kan nås via en mängd olika [Redis-klienter](http://redis.io/clients) för många programmeringsspråk. Varje klient har sin egen API som gör anrop till Redis cache instans med [Redis-kommandon](http://redis.io/commands).

Eftersom varje klient skiljer sig, det är inte en centraliserad klass referens på MSDN och upprätthåller sin egen referensdokumentation för varje klient. Förutom referensdokumentationen finns flera självstudier visar hur du kommer igång med Azure Redis Cache med olika språk och cache-klienter. Du hittar de här självstudierna i [hur du använder Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) och klicka på önskat språk från växlaren språk överst i artikeln.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Kan jag använda Azure Redis Cache som en PHP-sessionscache?
Ja, ange anslutningssträngen till din Azure Redis Cache-instans i om du vill använda Azure Redis Cache som en PHP-sessionscache `session.save_path`.

> [!IMPORTANT]
> När du använder Azure Redis Cache som en PHP-sessionscache, måste du URL: en koda säkerhetsnyckeln används för att ansluta till cachen, som visas i följande exempel:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Om nyckeln inte är URL-kodad, kan det hända att ett undantag med ett meddelande som liknar: `Failed to parse session.save_path`
>
>

Mer information om hur du använder Redis-Cache som en cache med PHP-session med PhpRedis-klienten finns i [hanterare för PHP-Session](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Vad är Redis-databaser?

Redis-databaser är en logisk uppdelning av data inom samma Redis-instans. Cache-minnet delas mellan alla databaser och faktiska minne förbrukningen av en viss databas beror på de nycklar/värden som lagras i databasen. Till exempel har en C6 cache 53 GB minne. Du kan välja att placera alla 53 GB i en databas eller du kan dela upp den mellan flera databaser. 

> [!NOTE]
> När du använder en Premium Azure Redis Cache med aktiverad klustring, är bara databasen 0 tillgänglig. Den här begränsningen är en inbyggd begränsning på Redis och är inte specifikt för Azure Redis Cache. Mer information finns i [behöver jag göra ändringar i mitt klientprogram för att använda kluster?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>När ska jag aktivera porten utan SSL för att ansluta till Redis?
Redis-server inte har inbyggt stöd SSL, men Azure Redis Cache har. Om du ansluter till Azure Redis Cache och klienten stöder SSL, som StackExchange.Redis, bör du använda SSL.

>[!NOTE]
>Icke-SSL-porten är inaktiverad som standard för nya Azure Redis Cache-instanser. Om klienten inte stöder SSL så måste du aktivera porten utan SSL genom att följa anvisningarna i den [tillgång portarna](cache-configure.md#access-ports) delen av den [konfigurera en cache i Azure Redis Cache](cache-configure.md) artikeln.
>
>

Redis verktyg som `redis-cli` fungerar inte med SSL-porten, men du kan använda ett verktyg som `stunnel` att på ett säkert sätt ansluta verktygen till SSL-porten genom att följa anvisningarna i den [Vi presenterar ASP.NET-Sessionstillståndsprovider för Redis Förhandsversionen](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogginlägg.

Anvisningar om hur du hämtar Redis-verktyg finns i den [hur kan jag köra Redis-kommandon?](#cache-commands) avsnittet.

### <a name="what-are-some-production-best-practices"></a>Vilka är några Metodtips för produktion?
* [Metodtips för StackExchange.Redis](#stackexchangeredis-best-practices)
* [Konfiguration och begrepp](#configuration-and-concepts)
* [Prestandatestning](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Metodtips för StackExchange.Redis
* Ange `AbortConnect` till false, låt ConnectionMultiplexer återansluta automatiskt. [Mer information finns här](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Återanvända ConnectionMultiplexer – inte skapa ett nytt lösenord för varje begäran. Den `Lazy<ConnectionMultiplexer>` mönstret [visas här](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) rekommenderas.
* Redis fungerar bäst med mindre värden, så fundera över hackning upp större data i flera nycklar. I [diskussionen Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb anses vara stora. Läs [i den här artikeln](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) för en exempel-problem som kan orsakas av stora värden.
* Konfigurera din [arbetstråd inställningar](#important-details-about-threadpool-growth) att undvika tidsgränser.
* Använd minst standard connectTimeout 5 sekunder. Det här intervallet skulle ge StackExchange.Redis tillräckligt med tid att återupprätta anslutningen i händelse av ett nätverk blip.
* Tänk på kostnaderna för prestanda för olika åtgärder som du kör. Exempelvis kan den `KEYS` kommandot är en O(n) åtgärd och bör undvikas. Den [redis.io plats](http://redis.io/commands/) har information kring komplexiteten tid för varje åtgärd som stöds. Klicka på varje kommando för att se komplexiteten för varje åtgärd.

#### <a name="configuration-and-concepts"></a>Konfiguration och begrepp
* Använd Standard eller Premium-nivån för produktionssystem. Basic-nivån är en enskild nod system med ingen replikering av data och inget serviceavtal. Kan också använda minst en C1 cache. C0 cacheminnen används vanligtvis för enkel utveckling och testning.
* Kom ihåg att Redis är en **InMemory-** datalager. Läs [i den här artikeln](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) så att du är medveten om scenarier där data gå förlorade.
* Utveckla dina system så att den kan hantera anslutning signaler [på grund av korrigeringar och redundans](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Prestandatestning
* Starta med hjälp av `redis-benchmark.exe` få en bild för möjliga genomflöde innan du skriver din egen perf testar. Eftersom `redis-benchmark` stöder inte SSL, måste du [aktivera icke-SSL-porten via Azure portal](cache-configure.md#access-ports) innan du kör testet. Exempel finns i [hur kan jag göra benchmark-test och testa prestanda hos mitt cacheminne?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Klienten VM används för testning bör vara i samma region som din Redis cache-instans.
* Du rekommenderas att använda Dv2-serien för virtuella datorer för din klient som de har bättre maskinvara och bör ge bästa resultat.
* Kontrollera att klienten VM som du har minst så mycket databehandling och funktioner som cache som du vill testa.
* Aktivera VRSS på klientdatorn om du använder Windows. [Mer information finns här](https://technet.microsoft.com/library/dn383582.aspx).
* Premium-nivån Redis-instanser har bättre nätverks-svarstid och dataflöde eftersom de kör på bättre maskinvara för både CPU och nätverk.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Vilka är några av överväganden när du använder vanliga Redis-kommandon?
* Du bör inte köra vissa Redis-kommandon som tar lång tid att slutföra utan att förstå effekten av dessa kommandon.
  * Kör till exempel inte den [nycklar](http://redis.io/commands/keys) kommando i produktionen medan det kan ta lång tid att returnera beroende på hur många nycklar. Redis är en single-threaded-server och den bearbetar kommandon ett i taget. Om du har andra kommandon som utfärdas efter nycklar kan behandlas de inte förrän Redis bearbetar KEYS-kommandot. Den [redis.io plats](http://redis.io/commands/) har information kring komplexiteten tid för varje åtgärd som stöds. Klicka på varje kommando för att se komplexiteten för varje åtgärd.
* Nyckelstorlekar - bör jag använda små nyckel/värde- eller stora nyckel/värde? I allmänhet beror på scenariot. Om din situation kräver större nycklar, kan du justera ConnectionTimeout och försök värden och justera omprövningslogiken. Ur en Redis-servern jobbnivå lägre värden ha bättre prestanda.
* Detta innebär inte att du inte kan lagra större värden i Redis; Du måste vara medveten om följande överväganden. Svarstiderna blir högre. Om du har en uppsättning data som är större och ett som är mindre, du kan använda flera ConnectionMultiplexer instanser, var och en konfigurerad med en annan uppsättning tidsgräns- och värden, enligt beskrivningen i föregående [vad gör StackExchange.Redis konfigurationsalternativ gör](#cache-configuration) avsnittet.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hur kan jag göra benchmark-test och testa prestanda hos mitt cacheminne?
* [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet. Du kan visa värdena på Azure-portalen eller [hämta och granska](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem med valfritt verktyg.
* Du kan använda redis-benchmark.exe för att belastningstesta Redis-servern.
* Kontrollera att belastningstest klienten och Redis-cache finns i samma region.
* Använd redis-cli.exe och övervaka med hjälp av kommandot INFO-cachen.
* Om inläsningen orsakar hög minnesfragmentering kan skala du upp till en större cachestorlek.
* Anvisningar om hur du hämtar Redis-verktyg finns i den [hur kan jag köra Redis-kommandon?](#cache-commands) avsnittet.

Följande kommandon ger ett exempel på hur du använder redis-benchmark.exe. Kör dessa kommandon från en virtuell dator i samma region som din cache för korrekta resultat.

* Testa Pipelined SET-frågor med hjälp av en nyttolast på 1 k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testa Pipelined GET-begäranden med en nyttolast på 1 kB.
  Obs: Köra UPPSÄTTNINGEN testa visas ovan först för att fylla cachen

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Viktig information om arbetstråd tillväxt
CLR-arbetstråd har två typer av trådar - ”Worker” och ”i/o-slutförandeport” (även kallat iocp skulle öppnas) trådar.

* Trådar som används för bearbetning av den `Task.Run(…)`, eller `ThreadPool.QueueUserWorkItem(…)` metoder. Dessa trådar används också av olika komponenter i CLR när arbete måste ske i en bakgrundstråd.
* Iocp skulle öppnas trådar används när asynkrona i/o händer (t.ex. läsning från nätverket).

Trådpoolen ger nya trådar eller trådar för i/o-slutförande på begäran (utan någon begränsning) tills den når inställningen ”minst” för varje typ av tråd. Som standard anges det minsta antalet trådar till antalet processorer på ett system.

När antalet befintliga (upptagen) trådar når ”minsta” antalet trådar, kommer arbetstråd begränsa den hastighet med vilken den lägger in nya trådar till en tråd per 500 millisekunder. Normalt om datorn får en burst av arbete som behöver en iocp skulle öppnas tråd kan bearbetas som fungerar mycket snabbt. Men om burst av arbete är större än den konfigurera ”minst”-inställningen har debiteras en fördröjning vid bearbetning av del av arbetet som arbetstråd väntar på något av följande saker händer.

1. En befintlig tråd blir kan bearbeta arbetet.
2. Ingen befintlig tråd blir gratis för 500ms, så skapas en ny tråd.

I princip innebär att när antalet upptagna trådar är större än Min trådar kan du förmodligen betalar en 500ms fördröjning innan nätverkstrafik bearbetas av programmet. Det är också viktigt att Observera att när en befintlig tråd är inaktiva längre än 15 sekunder (baserat på vad jag kommer ihåg), kommer att rensas bort och den här cykeln av ökning och minskning kan upprepa.

Om vi tittar på ett exempel felmeddelande från StackExchange.Redis (skapa 1.0.450 eller senare), visas den nu skriver ut arbetstråd statistik (se iocp skulle öppnas och ARBETSROLLER detaljer nedan).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

I exemplet ovan kan du se att det finns 6 upptagna trådar för iocp skulle öppnas tråd och systemet är konfigurerad för att tillåta 4 minimitrådar. I det här fallet klienten skulle förmodligen sett två 500 ms fördröjning eftersom 6 > 4.

Observera att StackExchange.Redis kan nå tidsgränser om tillväxten av iocp skulle öppnas eller WORKER-trådar hämtar begränsad.

### <a name="recommendation"></a>Rekommendation
Den här informationen rekommenderar vi att kunderna ange lägsta konfigurationen som värdet för iocp skulle öppnas och ARBETSROLLER trådar till något som är större än standardvärdet. Vi kan inte ge enkel information om vad det här värdet ska vara eftersom rätt värde för en ansökan blir för hög/låg för ett annat program. Den här inställningen kan också påverka prestanda för andra delar av komplicerade program så att varje kund behöver finjustera den här inställningen för deras specifika behov. En bra utgångspunkt är 200 eller 300, och sedan testa och justera efter behov.

Hur du konfigurerar den här inställningen:

* I ASP.NET, använder den [”minIoThreads” eller ”minWorkerThreads” konfigurationsinställning] [ "minIoThreads" configuration setting] under den `<processModel>` konfigurationselementet i web.config. Om du kör i Azure WebSites kan exponeras inte den här inställningen via konfigurationsalternativen. Men du bör fortfarande att kunna konfigurera den här inställningen programmässigt (se nedan) från din Application_Start-metod i global.asax.cs.

  > [!NOTE] 
  > Värdet som anges i den här konfigurationselement är en *per kärna* inställningen. Till exempel om du har en dator med 4 kärnor och vill att din minIOThreads inställningen ska vara 200 vid körning, använder du `<processModel minIoThreads="50"/>`.
  >

* Utanför ASP.NET, och Azure WebSites global.asax, använda den [ThreadPool.SetMinThreads (...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API: ET.

  > [!NOTE]
  > Värdet som angetts för detta API är en global inställning som påverkar hela AppDomain. Om du har en dator med 4 kärnor och ställa in minWorkerThreads och minIOThreads på 50 per CPU under körning, använder du ThreadPool.SetMinThreads (200, 200).

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Aktivera server GC att få större dataflöde på klienten när du använder StackExchange.Redis
Aktivera server GC kan optimera klienten och ger bättre prestanda och dataflöde när du använder StackExchange.Redis. Mer information om server GC och hur du aktiverar det finns i följande artiklar:

* [Aktivera server GC](https://msdn.microsoft.com/library/ms229357.aspx)
* [Grunderna i skräpinsamling](https://msdn.microsoft.com/library/ee787088.aspx)
* [Skräpinsamling och prestanda](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Prestandaöverväganden runt anslutningar

Varje prisnivå har olika begränsningar för klientanslutningar, minne och bandbredd. Med varje storleken på cacheminnet kan *upp till* ett visst antal anslutningar, varje anslutning till Redis är overhead kopplat till den. Ett exempel på sådana kostnader är användning av CPU och minne till följd av TLS/SSL-kryptering. Anslutningens maximala gränsen för en viss cachestorlek förutsätter en lågt belastade cache. Om läsa in från anslutningen overhead *plus* belastningen från Klientåtgärder överskrider kapaciteten för systemet, cacheminnet kan uppleva kapacitetsproblem även om du inte har överskridit anslutningsgränsen för den aktuella cachestorleken.

Läs mer om de olika anslutningar gränserna för varje nivå, [priser för Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/). Mer information om anslutningar och andra standardkonfigurationer som finns i [standard Redis server configuration](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hur övervakar jag hälsotillstånd och prestanda för mitt cacheminne?
Microsoft Azure Redis Cache-instanser kan övervakas i den [Azure-portalen](https://portal.azure.com). Du kan visa mått, fästa måttdiagram på startsidan, anpassa intervallet datum och tid för övervakning av diagram, lägga till och ta bort mått från diagrammen och ställa in aviseringar när vissa villkor är uppfyllda. Mer information finns i [övervaka Azure Redis Cache](cache-how-to-monitor.md).

Redis-Cache **resursmenyn** också innehåller flera verktyg för övervakning och felsökning dina cacheminnen.

* **Diagnostisera och lösa problem** ger information om vanliga problem och strategier för att lösa dem.
* **Resurshälsa** övervakar resursen och meddelar dig om den körs som förväntat. Läs mer om Azure Resource health service [översikt över hälsotillståndet för Azure Resource](../resource-health/resource-health-overview.md).
* **Ny supportförfrågan** ger alternativ för att öppna en supportbegäran för cacheminnet.

Dessa verktyg kan du övervaka hälsotillståndet för dina Azure Redis Cache-instanser och hjälper dig att hantera dina cachelagring program. Mer information finns i avsnittet ”Support och felsökning inställningar” i [så här konfigurerar du Azure Redis Cache](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Varför ser jag tidsgränser?
Timeout-fel inträffa i klienten som används för att kommunicera med Redis. När ett kommando skickas till Redis-servern använder kommandot är i kö och Redis-server så småningom hämtar kommandot och kör den. Men klienten kan tidsgräns under den här processen, och om den gör ett undantag utlöses på anropande sida. Mer information om hur du felsöker timeout-problem finns i [klientsidan felsökning](cache-how-to-troubleshoot.md#client-side-troubleshooting) och [tidsgränsundantag i StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Varför kopplades från en kunds från cachen?
Här följer några vanliga orsaken till ett cache-frånkoppling.

* Klientsidan orsaker
  * Klientprogrammet har omdistribueras.
  * Klientprogrammet utföra en åtgärd för skalning.
    * För molntjänster och Web Apps, kan detta bero på att automatisk skalning.
  * Nätverksnivån på klientsidan har ändrats.
  * Tillfälliga fel uppstod i klienten eller nätverksnoder mellan klienten och servern.
  * Bandbreddsgränser för tröskelvärde har uppnåtts.
  * CPU-bundna åtgärder tog för lång tid att slutföra.
* Serversidan orsaker
  * På den standard cache-erbjudande, initieras en redundansväxling från den primära noden till den sekundära noden i tjänsten Azure Redis Cache.
  * Azure korrigeringar instansen där cacheminnet har distribuerats
    * Detta kan vara för uppdateringar för Redis-server eller allmänt underhåll av virtuell dator.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Vilket Azure Cache-erbjudande är bäst för mig?
> [!IMPORTANT]
> Enligt förra årets [meddelande](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), Azure Managed Cache Service och Azure cachelagring i Rollinstanser **har tagits bort** på den 30 November 2016. Vår rekommendation är att använda [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Information om hur du migrerar finns i [migrera från Managed Cache Service till Azure Redis Cache](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Azure Redis Cache
Azure Redis Cache är allmänt tillgängligt i storlekar upp till 53 GB och har ett serviceavtal på 99,9% tillgänglighet. Den nya [premiumnivån](cache-premium-tier-intro.md) erbjuder storlekar upp till 530 GB och stöd för klustring, virtuellt nätverk och beständighet, med ett serviceavtal på 99,9%.

Azure Redis Cache ger kunderna möjlighet att använda en säker, dedikerad Redis-cache som hanteras av Microsoft. Med det här erbjudandet får du utnyttja den breda funktionsuppsättningen och ekosystemet som tillhandahålls av Redis och pålitliga värdtjänsten och övervakningen från Microsoft.

Till skillnad från traditionella cacheminnen som endast behandlar nyckel / värde-par, är Redis populärt för dess mycket högpresterande datatyper. Redis också stöder atomiska operationer för dessa typer, som att lägga till en sträng; öka värdet i en hash; push-överföra till en lista. databehandling uppsättning, skärning, union och skillnaden; eller lägga till medlemmen med högsta rangordning i en sorterad uppsättning. Andra funktioner är support för transaktioner, pub/sub, Lua-skript, nycklar med begränsad time-to-live, och konfigurationsinställningar som gör att Redis fungerar mer som en traditionell cache.

En annan viktig aspekt för Redis framgång är felfri, levande öppen källkod-ekosystemet som byggts kring den. Detta återges i mängd olika Redis-klienter som är tillgänglig mellan flera olika språk. Den här ekosystem och en mängd olika klienter kan du Azure Redis Cache som ska användas vid nästan alla arbetsbelastningsnivåer som du kan bygga inuti Azure.

Mer information om att komma igång med Azure Redis Cache finns i [hur du använder Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) och [dokumentation för Azure Redis Cache](index.md).

### <a name="managed-cache-service"></a>Managed Cache service
[Managed Cache service drogs tillbaka den 30 November 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Arkiverad dokumentation finns [arkiverade Managed Cache Service-dokumentationen](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Cache i roller
[Cachelagring i Rollinstanser drogs tillbaka den 30 November 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Arkiverad dokumentation finns [arkiverad dokumentation för Cache i roller](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
