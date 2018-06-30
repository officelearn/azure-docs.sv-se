---
title: Azure Redis-Cache vanliga frågor och svar | Microsoft Docs
description: Läs svaren på vanliga frågor, mönster och bästa praxis för Azure Redis-Cache
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
ms.openlocfilehash: 6c308205c5adb05f4c7e1668c67adea414020ea2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113280"
---
# <a name="azure-redis-cache-faq"></a>Vanliga frågor och svar för Azure Redis Cache
Lär dig svar på vanliga frågor, mönster och bästa praxis för Azure Redis-Cache.

## <a name="what-if-my-question-isnt-answered-here"></a>Vad gör jag om min fråga inte besvaras här?
Om din fråga inte finns med här kan för oss berätta och vi hjälper dig att hitta ett svar.

* Du kan skicka en fråga i kommentarerna i slutet av dessa vanliga frågor och interagera med Azure Cache-teamet och andra gruppmedlemmar om den här artikeln.
* Om du vill nå en bredare publik kan du skicka en fråga på den [Azure Cache MSDN-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) och interagera med Azure Cache-teamet och andra medlemmar i gruppen.
* Om du vill göra en funktionsbegäran om kan du skicka din begäran och idéer [Azure Redis-Cache User Voice](https://feedback.azure.com/forums/169382-cache).
* Du kan också skicka ett e-postmeddelande till oss på [Azure Cache externa Feedback](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Grunderna i Azure Redis-Cache
Vanliga frågor om i det här avsnittet beskriver några av grunderna i Azure Redis-Cache.

* [Vad är Azure Redis Cache?](#what-is-azure-redis-cache)
* [Hur kan jag igång med Azure Redis-Cache?](#how-can-i-get-started-with-azure-redis-cache)

Följande vanliga frågor och svar täcker grundläggande koncept och frågor om Azure Redis-Cache och besvaras i andra avsnitt i vanliga frågor och svar.

* [Vilket erbjudande och vilken storlek ska jag använda för Redis-cache?](#what-redis-cache-offering-and-size-should-i-use)
* [Vilka Redis cache-klienter kan använda?](#what-redis-cache-clients-can-i-use)
* [Finns det en lokal emulator för Azure Redis-Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Hur övervakar jag hälsotillstånd och prestanda för Mina cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Planera vanliga frågor och svar
* [Vilket erbjudande och vilken storlek ska jag använda för Redis-cache?](#what-redis-cache-offering-and-size-should-i-use)
* [Azure Redis-Cache-prestanda](#azure-redis-cache-performance)
* [I vilken region bör jag hitta min cache?](#in-what-region-should-i-locate-my-cache)
* [Hur är debiteras för Azure Redis-Cache?](#how-am-i-billed-for-azure-redis-cache)
* [Kan jag använda Azure Redis-Cache med Azure offentliga moln, Kina Azure-molnet eller Microsoft Azure Tyskland?](#can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Utveckling och svar
* [Vad gör konfigurationsalternativ StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Vilka Redis cache-klienter kan använda?](#what-redis-cache-clients-can-i-use)
* [Finns det en lokal emulator för Azure Redis-Cache?](#is-there-a-local-emulator-for-azure-redis-cache)
* [Hur kör Redis kommandon?](#how-can-i-run-redis-commands)
* [Varför har inte en MSDN-biblioteket klassreferens som del av andra Azure-tjänster i Azure Redis-Cache?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Kan jag använda Azure Redis-Cache som en PHP-sessionscachen?](#can-i-use-azure-redis-cache-as-a-php-session-cache)
* [Vad är Redis databaser?](#what-are-redis-databases)

## <a name="security-faqs"></a>Vanliga frågor om Security
* [När bör jag aktivera porten utan SSL för att ansluta till Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Produktion vanliga frågor och svar
* [Vilka är några rekommendationer för produktion?](#what-are-some-production-best-practices)
* [Vilka är några av överväganden när du använder vanliga Redis kommandon?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hur kan mäta och testa prestanda för Mina cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Viktig information om arbetstråd tillväxt](#important-details-about-threadpool-growth)
* [Aktivera servern GC få mer genomströmning på klienten när du använder StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Prestandaöverväganden runt anslutningar](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Övervakning och felsökning av vanliga frågor och svar
Vanliga frågor om i det här avsnittet beskriver vanliga övervakning och felsökning frågor. Mer information om övervakning och felsökning Azure Redis-Cache-instanser finns [hur du övervakar Azure Redis-Cache](cache-how-to-monitor.md) och [felsökning av Azure Redis-Cache](cache-how-to-troubleshoot.md).

* [Hur övervakar jag hälsotillstånd och prestanda för Mina cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Varför ser jag tidsgränser](#why-am-i-seeing-timeouts)
* [Varför har min klient kopplats från cacheminnet?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Tidigare Cache erbjudande vanliga frågor och svar
* [Vilka Azure Cache-erbjudande är rätt för mig?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-redis-cache"></a>Vad är Azure Redis Cache?
Azure Redis Cache baseras på den populära [Redis-cachen](http://redis.io) med öppen källkod. Det ger dig tillgång till en säker, dedikerad Redis-cache, hanteras av Microsoft och som är tillgänglig från alla program i Azure. En detaljerad översikt finns i [Azure Redis-Cache](https://azure.microsoft.com/services/cache/) produktsidan på Azure.com.

### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Hur kan jag igång med Azure Redis-Cache?
Det finns flera sätt som du kan komma igång med Azure Redis-Cache.

* Du kan checka ut en av våra självstudier som är tillgängliga för [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), och [Python](cache-python-get-started.md).
* Du kan titta på [hur du skapar högpresterande appar med Microsoft Azure Redis-Cache](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Du kan checka ut i dokumentationen för klienter för klienter som matchar ditt projekt och se hur du använder Redis programmeringsspråk. Det finns många Redis-klienter som kan användas med Azure Redis-Cache. En lista över Redis-klienter finns [ http://redis.io/clients ](http://redis.io/clients).

Om du inte redan har ett Azure-konto, kan du:

* [Öppna ett Azure-konto kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Du får kredit som kan användas för att prova Azure-tjänster som normalt inte är kostnadsfria. Du kan behålla kontot även efter att krediten är slut och använda kostnadsfria Azure-tjänster och -funktioner.
* [Aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

<a name="cache-size"></a>

### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Vilket erbjudande och vilken storlek ska jag använda för Redis-cache?
Varje Azure Redis-Cache innehåller olika nivåer av **storlek**, **bandbredd**, **hög tillgänglighet**, och **SLA** alternativ.

Följande är att tänka på för att välja ett erbjudande för cachen.

* **Minne**: nivåer i Basic och Standard erbjuder 250 MB – 53 GB. Premium-nivån ger upp till 530 GB. Mer information finns i [priser för Azure Redis-Cache](https://azure.microsoft.com/pricing/details/cache/).
* **Nätverksprestanda**: Om du har en arbetsbelastning som kräver hög genomströmning Premium-nivån ger högre bandbredd jämfört med Standard- eller Basic. Inom varje nivå har större storlek cacheminnen också mer bandbredd på grund av den underliggande virtuella datorn som är värd för cachen. Finns det [följande tabell](#cache-performance) för mer information.
* **Genomströmning**: I Premium-nivån ger högsta tillgängliga genomströmning. Om cacheserver eller klienten når gränser för bandbredd, kan du få timeout på klientsidan. Mer information finns i följande tabell.
* **Hög tillgänglighet/SLA**: Azure Redis-Cache garanterar att Standard/Premium cache är tillgänglig med minst 99,9% av tiden. Läs mer om våra SLA i [priser för Azure Redis-Cache](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). SLA omfattar bara anslutningen till Cache-slutpunkter. Serviceavtalet täcker inte skydd mot dataförlust. Vi rekommenderar att du använder funktionen Redis data beständiga i Premium-nivån för att öka återhämtningsförmågan mot dataförlust.
* **Redis-Datapersistence**: I Premium-nivån kan du bevara Cachedata i ett Azure Storage-konto. Alla data lagras i en grundläggande/Standard cache endast i minnet. Om det kan underliggande infrastruktur problem det vara potentiell dataförlust. Vi rekommenderar att du använder funktionen Redis data beständiga i Premium-nivån för att öka återhämtningsförmågan mot dataförlust. Azure Redis-Cache har RDB och AOF (kommer snart) alternativ i Redis-persistence. Mer information finns i [hur du konfigurerar persistence för Premium Azure Redis-Cache](cache-how-to-premium-persistence.md).
* **Redis-kluster**: skapa cachelagrar större än 53 GB och Fragmentera data över flera Redis-noder, du kan använda Redis-kluster som är tillgängliga i Premium-nivån. Varje nod består av två primära/replik cache för hög tillgänglighet. Mer information finns i [Konfigurera klustring för premium Azure Redis-cache](cache-how-to-premium-clustering.md).
* **Förbättrad säkerhet och nätverk isolerade**: distribution av Azure virtuella nätverk (VNET) ger förbättrad säkerhet och isolering för Azure Redis-Cache, samt undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten. Mer information finns i [Konfigurera Virtual Network-stöd för premium Azure Redis-cache](cache-how-to-premium-vnet.md).
* **Konfigurera Redis**: I både Standard- och Premium-nivåer, kan du konfigurera Redis för Keyspace-meddelanden.
* **Maximalt antal klientanslutningar**: I Premium-nivån erbjuder det maximala antalet klienter som kan ansluta till Redis med ett högre antal anslutningar för större storlek. Antalet anslutningar som är tillgängliga för en klustrad cache ökar inte kluster. Mer information finns i [priser för Azure Redis-Cache](https://azure.microsoft.com/pricing/details/cache/).
* **Dedikerade Redis Server Core**: I Premium-nivån alla storlekar för cache har en dedikerad kärna för Redis. I Basic/Standard nivåer, C1 storlek och senare har en dedikerad kärna för Redis-servern.
* **Redis är enkeltrådad** så att ha fler än två kärnor ger inte nytta över med två kärnor, men större VM-storlekar har vanligtvis mer bandbredd än är mindre. Om cacheserver eller klienten når gränser för bandbredd, får du timeout på klientsidan.
* **Prestandaförbättringar**: Cacheminnena i Premium-nivån har distribuerats på maskinvara som har snabba processorer kan ge bättre prestanda jämfört med nivån Basic eller Standard. Premium-nivån cacheminnen har högre genomflöde och lägre latens.

<a name="cache-performance"></a>

### <a name="azure-redis-cache-performance"></a>Azure Redis-Cache-prestanda
I följande tabell visas de maximala bandbredd värden som observerats vid testning av olika storlekar på Standard och Premium cachelagrar med `redis-benchmark.exe` från ett Iaas-VM mot Azure Redis-Cache-slutpunkten. För SSL-genomströmning används redis-prestandamått med stunnel för att ansluta till Azure Redis-Cache-slutpunkten.

>[!NOTE] 
>Dessa värden är inte garanterat och det finns inga SLA för dessa siffror, men bör vara vanliga. Du bör läsa testa programmet att fastställa rätt cachestorleken för ditt program.
>Dessa värden kan ändras när vi publicerar nya resultaten regelbundet.
>

Vi kan upprätta följande slutsatser från den här tabellen:

* Dataflöde för cacheminnen som har samma storlek är högre upp i Premium-nivån jämfört med standardnivån. Med en 6 GB Cache är genomflödet i P1 exempelvis 180 000 RPS jämfört med 100 000 för C3.
* Med Redis-kluster, ökar genomflödet linjärt när du ökar antalet delar (noder) i klustret. Till exempel om du skapar ett P4 kluster på 10 delar det tillgängliga genomflödet är 400 000 * 10 = 4 miljoner RPS.
* Dataflöde för större viktiga storlekar är högre upp i Premium-nivån jämfört med standardnivån.

| Prisnivå | Storlek | Processorkärnor | Tillgänglig bandbredd | 1 KB storlek | 1 KB storlek |
| --- | --- | --- | --- | --- | --- |
| **Standard cache-storlekar** | | |**Megabit per sekund (Mb/s) / megabyte per sekund (MB/s)** |**Förfrågningar per andra (RPS) icke-SSL** |**Förfrågningar per andra (RPS) SSL** |
| C0 |250 MB |Delad |100 / 12.5 |15,000 |7 500 |
| C1 |1 GB |1 |500 / 62.5 |38,000 |20,720 |
| C2 |2,5 GB |2 |500 / 62.5 |41,000 |37,000 |
| C3 |6 GB |4 |1000 / 125 |100,000 |90,000 |
| C4 |13 GB |2 |500 / 62.5 |60,000 |55,000 |
| C5 |26 GB |4 |1,000 / 125 |102 000 kr |93,000 |
| C6 |53 GB |8 |2,000 / 250 |126,000 |120,000 |
| **Premium cache-storlekar** | |**CPU-kärnor per Fragmentera** | **Megabit per sekund (Mb/s) / megabyte per sekund (MB/s)** |**Förfrågningar per andra (RPS) icke-SSL, per Fragmentera** |**Förfrågningar per andra (RPS) SSL per Fragmentera** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13 GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26 GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53 GB |8 |6,000 / 750 |400,000 |373,000 |

Instruktioner om hur du konfigurerar stunnel eller hämta Redis-verktyg som `redis-benchmark.exe`, finns det [hur kan jag köra Redis kommandon?](#cache-commands) avsnitt.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>I vilken region bör jag hitta min cache?
Hitta din Azure Redis-Cache för bästa prestanda och lägsta fördröjningen i samma region som din cache-klientprogrammet.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-redis-cache"></a>Hur är debiteras för Azure Redis-Cache?
Priser för Azure Redis-Cache är [här](https://azure.microsoft.com/pricing/details/cache/). Prissättningssidan visar priser som en timvis frekvens. Cacheminnen debiteras på grundval av per minut från den tidpunkt då cachen har skapats tills en cache har tagits bort. Det finns inget alternativ för stoppas eller pausas fakturering en cache.

### <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Kan jag använda Azure Redis-Cache med Azure offentliga moln, Kina Azure-molnet eller Microsoft Azure Tyskland?
Ja, Azure Redis-Cache är tillgängliga i Azure offentliga moln, Kina Azure-molnet och Microsoft Azure Tyskland. URL: er för att komma åt och hantera Azure Redis-Cache är olika i dessa moln jämfört med Azures offentliga moln. 

| Molnet   | DNS-Suffix för Redis            |
|---------|---------------------------------|
| Offentligt  | *.redis.cache.windows.net       |
| USA-förvaltad region  | *.redis.cache.usgovcloudapi.net |
| Tyskland | *.redis.cache.cloudapi.de       |
| Kina   | *.redis.cache.chinacloudapi.cn  |

Mer information om att tänka på när du använder Azure Redis-Cache med andra moln finns i följande länkar.

- [Azure Government - databaser i Azure Redis-Cache](../azure-government/documentation-government-services-database.md#azure-redis-cache)
- [Azure Kina Cloud - Azure Redis-Cache](https://www.azure.cn/documentation/services/redis-cache/)
- [Microsoft Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)

Information om hur du använder Azure Redis-Cache med PowerShell i Azure offentliga moln, Kina Azure-molnet och Microsoft Azure Tyskland finns [hur du ansluter till andra moln - Azure Redis-Cache PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Vad gör konfigurationsalternativ StackExchange.Redis?
StackExchange.Redis har många alternativ. Det här avsnittet innehåller information om några av de vanliga inställningarna. Mer detaljerad information om alternativ för StackExchange.Redis finns [StackExchange.Redis configuration](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Beskrivning | Rekommendation |
| --- | --- | --- |
| AbortOnConnectFail |När inställd på Sant anslutningen kommer inte att återansluta efter ett nätverksfel. |Värdet false och låt StackExchange.Redis återansluta automatiskt. |
| ConnectRetry |Antalet gånger Upprepa anslutningsförsök under inledande ansluta. |Se följande information för vägledning. |
| ConnectTimeout |Tidsgräns i ms för att ansluta operations. |Se följande information för vägledning. |

Oftast räcker standardvärdena för klienten. Du kan finjustera alternativ baserat på din arbetsbelastning.

* **Antal försök**
  * För ConnectRetry och ConnectTimeout är allmänna riktlinjer att misslyckas snabb och försök igen. Den här vägledningen är baserad på din arbetsbelastning och hur lång tid på genomsnittlig det tar för en klient att utfärda ett Redis-kommando och ett svar.
  * Låt StackExchange.Redis återansluta automatiskt i stället för att kontrollera anslutningsstatus för och ansluta dig själv. **Undvik att använda egenskapen ConnectionMultiplexer.IsConnected**.
  * Snowballing - ibland kan du stöta på ett problem där du försöker igen och försök snowball och aldrig återställs. Om snowballing inträffar bör du använda en exponentiell backoff försök algoritm som beskrivs i [försök allmänna riktlinjer](../best-practices-retry-general.md) publicerats av Microsoft Patterns & Practices-gruppen.
* **Timeout-värden**
  * Överväg att din arbetsbelastning och ange värden i enlighet med detta. Om du lagrar stora värden, ange tidsgränsen till ett högre värde.
  * Ange `AbortOnConnectFail` till false och låta StackExchange.Redis återanslutning av du.
  * Använda en enda instans ConnectionMultiplexer för programmet. Du kan använda en LazyConnection för att skapa en instans som returneras av en Anslutningsegenskapen enligt [ansluta till cacheminnet med hjälp av klassen ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ange den `ConnectionMultiplexer.ClientName` egenskapen till en app-instansen unikt namn för att ställa diagnoser.
  * Använda flera `ConnectionMultiplexer` instanser för anpassade arbetsbelastningar.
      * Om du har olika belastningen i ditt program kan du följa den här modellen. Exempel:
      * Du kan ha en multiplexor för att hantera stora nycklar.
      * Du kan ha en multiplexor för att hantera små nycklar.
      * Du kan ange olika värden för timeout och försök logiken i varje ConnectionMultiplexer som du använder.
      * Ange den `ClientName` egenskapen på varje multiplexor för bättre diagnostik.
      * Den här vägledningen kan leda till mer effektiv latens per `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Vilka Redis cache-klienter kan använda?
En av fördelarna med Redis är att det finns många klienter som stöder många olika programmeringsspråk. En aktuell lista över klienter finns i [Redis-klienter](http://redis.io/clients). Självstudier som täcker flera olika språk och -klienter finns [hur du använder Azure Redis-Cache](cache-dotnet-how-to-use-azure-redis-cache.md) och klicka på önskat språk från språk switcher överst i artikeln.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Finns det en lokal emulator för Azure Redis-Cache?
Det finns inga lokala emulator för Azure Redis-Cache, men du kan köra MSOpenTech version av redis-server.exe från den [Redis kommandoradsverktyg](https://github.com/MSOpenTech/redis/releases/) på lokalt datorn och ansluta till den att få en liknande miljö till en lokal cache-emulator som visas i följande exempel:

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


Du kan också konfigurera en [redis.conf](http://redis.io/topics/config) fil som passar den [standardinställningar cache](cache-configure.md#default-redis-server-configuration) för din online Azure Redis-Cache om du vill.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hur kör Redis kommandon?
Du kan använda någon av de kommandon som finns på [Redis kommandon](http://redis.io/commands#) förutom de kommandon som finns på [Redis-kommandon som inte stöds i Azure Redis-Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Du har flera alternativ för att köra Redis-kommandon.

* Om du har en Standard eller Premium-cache, kan du köra Redis-kommandon med hjälp av den [Redis-konsolen](cache-configure.md#redis-console). Redis-konsolen ger ett säkert sätt att köra Redis-kommandon i Azure-portalen.
* Du kan också använda Redis-kommandoradsverktyg. Utför följande steg för att använda dem:
* Hämta den [Redis kommandoradsverktyg](https://github.com/MSOpenTech/redis/releases/).
* Ansluta till en cache med hjälp av `redis-cli.exe`. Skicka in den cache-slutpunkten med den -h växla och nyckeln med - a som visas i följande exempel:
* `redis-cli -h <redis cache name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Kommandoradsverktyg för Redis fungerar inte med SSL-porten, men du kan använda ett verktyg som `stunnel` för att ansluta säkert verktygen till SSL-porten genom att följa anvisningarna i den [om ASP.NET Sessionstillståndsprovider för Redis-förhandsgranskning Versionen](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogginlägg.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Varför har inte en MSDN-biblioteket klassreferens som del av andra Azure-tjänster i Azure Redis-Cache?
Microsoft Azure Redis-Cache är baserad på populära öppen källkod Redis-Cache och kan kommas åt av en mängd olika [Redis-klienter](http://redis.io/clients) för många programmeringsspråk. Varje klient har sin egen API som gör anrop till Redis cache instans med [Redis kommandon](http://redis.io/commands).

Eftersom varje klient är olika, det är inte en centraliserad klassreferens på MSDN och upprätthåller sin egen referensdokumentationen för varje klient. Förutom referensdokumentationen finns det flera självstudiekurser visar hur du kommer igång med Azure Redis-Cache med hjälp av olika språk och cacheklienter. Du hittar de här kurserna i [hur du använder Azure Redis-Cache](cache-dotnet-how-to-use-azure-redis-cache.md) och klicka på önskat språk från språk switcher överst i artikeln.

### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Kan jag använda Azure Redis-Cache som en PHP-sessionscachen?
Ja, om du vill använda Azure Redis-Cache som en PHP-sessionscachen ange anslutningssträngen till din Azure Redis-Cache-instans i `session.save_path`.

> [!IMPORTANT]
> När du använder Azure Redis-Cache som en PHP-sessionscachen, måste du URL koda säkerhetsnyckeln används för att ansluta till i cacheminnet som visas i följande exempel:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Om nyckeln inte är URL-kodade, kan du få ett undantag med meddelandet som: `Failed to parse session.save_path`
>
>

Mer information om hur du använder Redis-Cache som en PHP-sessionscachen med PhpRedis-klienten finns [PHP Session hanteraren](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Vad är Redis databaser?

Redis-databaser är en logisk uppdelning av data i samma Redis-instans. Cache-minnet delas mellan alla databaser och faktiskt minne förbrukningen av en viss databas beror på de nycklar/värden som lagras i databasen. Till exempel har en C6 cache 53 GB minne. Du kan välja att placera alla 53 GB i en databas eller dela den upp mellan flera databaser. 

> [!NOTE]
> När du använder en Premium Azure Redis-Cache med aktiverad klustring, är endast databasen 0 tillgänglig. Den här begränsningen är en inbyggd begränsning på Redis och är inte specifika för Azure Redis-Cache. Mer information finns i [behöver jag göra ändringar i client-program att använda kluster?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>När bör jag aktivera porten utan SSL för att ansluta till Redis?
Redis-servern stöder inte internt SSL, men Azure Redis-Cache har. Om du ansluter till Azure Redis-Cache och klienten stöder SSL, som StackExchange.Redis, bör du använda SSL.

>[!NOTE]
>Icke-SSL-porten är inaktiverad som standard för nya Azure Redis-Cache-instanser. Om klienten inte stöder SSL, så du måste aktivera porten utan SSL genom att följa anvisningarna i den [tillgång portarna](cache-configure.md#access-ports) avsnitt i den [konfigurera en cache i Azure Redis-Cache](cache-configure.md) artikel.
>
>

Redis verktyg som `redis-cli` fungerar inte med SSL-porten, men du kan använda ett verktyg som `stunnel` för att ansluta säkert verktygen till SSL-porten genom att följa anvisningarna i den [om ASP.NET Sessionstillståndsprovider för Redis Förhandsgranska versionen](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogginlägg.

Anvisningar om hur du hämtar Redis-verktyg finns i [hur kan jag köra Redis kommandon?](#cache-commands) avsnitt.

### <a name="what-are-some-production-best-practices"></a>Vilka är några rekommendationer för produktion?
* [Metodtips för StackExchange.Redis](#stackexchangeredis-best-practices)
* [Konfiguration och begrepp](#configuration-and-concepts)
* [Prestandatestning](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Metodtips för StackExchange.Redis
* Ange `AbortConnect` till false, låt ConnectionMultiplexer återansluta automatiskt. [Mer information finns här](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Återanvända ConnectionMultiplexer - inte skapa en ny för varje begäran. Den `Lazy<ConnectionMultiplexer>` mönster [visas här](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) rekommenderas.
* Redis fungerar bäst med lägre värden, så fundera över hackning upp större data till flera nycklar. I [diskussionen Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb anses vara stora. Läs [i den här artikeln](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) för en exempel-problem som kan orsakas av stora värden.
* Konfigurera din [arbetstråd inställningar](#important-details-about-threadpool-growth) att undvika timeout.
* Använd minst standard connectTimeout 5 sekunder. Det här intervallet skulle ge StackExchange.Redis tillräckligt länge för att återupprätta anslutningen vid en blip i nätverket.
* Tänk på prestanda kostnaderna för olika åtgärder som du kör. Till exempel den `KEYS` kommandot är en O(n) åtgärd och bör undvikas. Den [redis.io plats](http://redis.io/commands/) har informationen runt komplexitet tid för varje åtgärd som det stöder. Klicka på varje kommando för att se komplexitet för varje åtgärd.

#### <a name="configuration-and-concepts"></a>Konfiguration och begrepp
* Använd Standard eller Premium-nivån för produktionssystem. Den grundläggande nivån är en enskild nod system med ingen datareplikering och inga SLA. Använd minst en C1 cache. C0 cacheminnen används vanligtvis för scenarier med enkel utveckling och testning.
* Kom ihåg att Redis är en **i minnet** datalagret. Läs [i den här artikeln](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) så att du är medveten om scenarier där data kan gå förlorade.
* Utveckla systemet så att den kan hantera anslutning signaler [på grund av uppdatering och växling vid fel](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Prestandatestning
* Starta med hjälp av `redis-benchmark.exe` att få en bild av möjliga genomströmningen innan skriva egna perf tester. Eftersom `redis-benchmark` har inte stöd för SSL, måste du [aktivera icke-SSL-porten via Azure portal](cache-configure.md#access-ports) innan du kör testet. Exempel finns [hur kan jag mäta och testa prestanda för Mina cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Klienten VM som används för att testa måste vara i samma region som Redis-cacheinstansen.
* Vi rekommenderar att du använder Dv2 VM-serien för din klient eftersom de har bättre maskinvara och ge bästa resultat.
* Kontrollera att klienten VM som du har minst så mycket datoranvändning och funktioner som cache som du vill testa.
* Aktivera skalning på klientdatorn om du är i Windows. [Mer information finns här](https://technet.microsoft.com/library/dn383582.aspx).
* Premium-nivån Redis instanser har bättre nätverks-svarstid och genomströmning eftersom de kör på bättre maskinvara för både CPU och nätverk.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Vilka är några av överväganden när du använder vanliga Redis kommandon?
* Du bör inte köra vissa Redis-kommandon som tar lång tid att slutföra utan att förstå effekten av dessa kommandon.
  * Kör till exempel inte den [nycklar](http://redis.io/commands/keys) kommandot i produktion som det kan ta lång tid att returnera beroende på antalet nycklar. Redis är en enkeltrådig server och den bearbetar kommandon ett i taget. Om du har andra kommandon som utfärdats efter nycklar bearbetas de inte förrän Redis bearbetar kommandot nycklar. Den [redis.io plats](http://redis.io/commands/) har informationen runt komplexitet tid för varje åtgärd som det stöder. Klicka på varje kommando för att se komplexitet för varje åtgärd.
* Viktiga storlek – ska du använda små nyckel-värden eller stora nyckel-värden? I allmänhet beror på scenariot. Om din situation kräver större nycklar, kan du justera ConnectionTimeout och försök värden och justera logik för omprövning. Ur Redis server observeras lägre värden ha bättre prestanda.
* Dessa överväganden innebär inte att du kan lagra större värden i Redis; Du måste vara medveten om följande. Svarstiderna blir högre. Om du har en uppsättning data som är större och ett som är mindre, du kan använda flera ConnectionMultiplexer instanser, var konfigurerad med en annan uppsättning värden för timeout och försök igen, enligt beskrivningen i föregående [gör StackExchange.Redis konfigurationsalternativ gör](#cache-configuration) avsnitt.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hur kan mäta och testa prestanda för Mina cache?
* [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet. Du kan visa värdena på Azure-portalen eller [hämta och granska](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem med valfritt verktyg.
* Du kan använda redis-benchmark.exe för att läsa in test Redis-servern.
* Se till att belastningen testa klienten och Redis-cache i samma region.
* Använd redis-cli.exe och övervaka med hjälp av kommandot INFO-cachen.
* Om din belastningen orsakar hög minnesfragmenteringen kan skala du upp till en större cachestorlek.
* Anvisningar om hur du hämtar Redis-verktyg finns i [hur kan jag köra Redis kommandon?](#cache-commands) avsnitt.

Följande kommandon är ett exempel på hur redis benchmark.exe. För korrekt resultat bör du köra dessa kommandon från en virtuell dator i samma region som ditt cacheminne.

* Testa pipeline SET-begäranden med en 1 k-nyttolast

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testa pipeline hämta begäranden med en 1 k nyttolast.
  Obs: Körs UPPSÄTTNINGEN testa visas ovan först för att fylla i cachen

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Viktig information om arbetstråd tillväxt
CLR-arbetstråd har två typer av trådar - ”Worker” och ”i/o-slutförandeport” (även kallat iocp skulle öppnas) trådar.

* Trådar som används när för sådant som bearbetning `Task.Run(…)` eller `ThreadPool.QueueUserWorkItem(…)` metoder. Dessa trådar används också av olika komponenter i CLR när arbetet som behöver göras på en bakgrundstråd.
* Iocp skulle öppnas trådar används när asynkrona i/o händer (t.ex. läsning från nätverket).

Trådpoolen ger nya arbetstrådar eller i/o-slutförande trådar på begäran (utan någon begränsning) tills ”minsta” inställningen för varje typ av tråd. Det minsta antalet trådar är som standard, antalet processorer på ett system.

När antalet trådar som befintliga (upptagen) träffar ”minsta” antalet trådar, kommer arbetstråd begränsning den hastighet med vilken den lägger in nya trådar för att en tråd per 500 millisekunder. Normalt om datorn hämtar en burst av arbete som behöver en iocp skulle öppnas tråd, bearbetas som fungerar mycket snabbt. Men om burst av arbete är större än den konfigurerade ”minsta” har kommer det att vissa fördröjning vid bearbetning av del av arbetet som ThreadPool väntar på något av följande händer.

1. En befintlig tråd blir ledigt för att bearbeta arbetet.
2. Ingen befintlig tråd blir gratis för 500ms, så skapas en ny tråd.

I princip betyder att när antalet trådar som upptagen är större än minsta trådar du förmodligen betalar en 500ms fördröjning innan nätverkstrafik bearbetas av programmet. Det är också viktigt att Observera att när en befintlig tråd är inaktiva längre än 15 sekunder (baserat på vad jag kommer ihåg), kommer att rensas bort och upprepa den här cykeln av ökning och minskning.

Om vi tittar på ett felmeddelande med exempel från StackExchange.Redis (skapa 1.0.450 eller senare), visas nu ut arbetstråd statistik (iocp skulle öppnas och WORKER mer information finns nedan).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Du kan se att tråd iocp skulle öppnas för det finns 6 upptagen trådar och systemet är konfigurerad för att tillåta 4 minimitrådar i föregående exempel. I så fall måste klienten skulle förmodligen sett två 500 ms fördröjning eftersom 6 > 4.

Observera att StackExchange.Redis kan träffa tidsgränser om tillväxt iocp skulle öppnas eller WORKER trådar hämtar begränsas.

### <a name="recommendation"></a>Rekommendation
Den här informationen rekommenderar vi starkt att kunder värdet minimikonfiguration för iocp skulle öppnas och WORKER trådar till något som är större än standardvärdet. Vi kan inte ge enkel riktlinjer för vad det här värdet bör vara eftersom rätt värde för ett program kommer att vara för hög/låg för ett annat program. Den här inställningen kan också påverka prestandan för andra delar av komplicerade program så att varje kund behöver finjustera inställningen till deras specifika behov. En bra utgångspunkt är 200 eller 300, och sedan testa och justera vid behov.

Hur du konfigurerar den här inställningen:

* ASP.NET använder den [”minIoThreads” eller ”minWorkerThreads” Konfigurationsinställningen] [ "minIoThreads" configuration setting] under den `<processModel>` konfigurationselementet i web.config. Om du kör inuti Azure WebSites, visas inte den här inställningen via konfigurationsalternativen. Men du måste fortfarande att kunna konfigurera den här inställningen programmässigt (se nedan) från Application_Start-metoden i global.asax.cs.

  > [!NOTE] 
  > Värdet som anges i den här konfigurationselement är en *per kärnor* inställningen. Till exempel om du har en dator med 4 kärnor och vill inställningen minIOThreads till 200 vid körning, du skulle använda `<processModel minIoThreads="50"/>`.
  >

* Utanför ASP.NET och Azure WebSites global.asax, Använd [ThreadPool.SetMinThreads (...)] (https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

  > [!NOTE]
  > Värdet som angetts för detta API är en global inställning som påverkar hela AppDomain. Om du har en dator med 4 kärnor och ange minWorkerThreads och minIOThreads till 50 per CPU under körning, använder du ThreadPool.SetMinThreads (200, 200).

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Aktivera servern GC få mer genomströmning på klienten när du använder StackExchange.Redis
Aktivera servern GC kan optimera klienten och ger bättre prestanda och genomflöde när du använder StackExchange.Redis. Mer information om server GC och hur du aktiverar det finns i följande artiklar:

* [Aktivera servern GC](https://msdn.microsoft.com/library/ms229357.aspx)
* [Grunderna i skräpinsamling](https://msdn.microsoft.com/library/ee787088.aspx)
* [Skräpinsamling och prestanda](https://msdn.microsoft.com/library/ee851764.aspx)


### <a name="performance-considerations-around-connections"></a>Prestandaöverväganden runt anslutningar

Varje prisnivå har olika begränsningar för klientanslutningar, minne och bandbredd. Med varje cachens storlek kan *upp till* ett visst antal anslutningar, varje anslutning till Redis har associeras med den. Ett exempel på sådana kostnader är processor- och minnesanvändning på grund av TLS/SSL-kryptering. Anslutningens maximala gränsen för en given cachestorlek förutsätter en lågt belastade cache. Om att läsa in från anslutningen kostnader *plus* belastningen från Klientåtgärder överskrider kapaciteten för systemet, cachen kan ha kapacitet problem, även om du inte har överskridit gränsen för antal anslutningar för den aktuella cachestorleken.

Mer information om olika anslutningar gränserna för varje nivå finns [priser för Azure Redis-Cache](https://azure.microsoft.com/pricing/details/cache/). Mer information om anslutningar och andra standardkonfigurationer som finns [standard Redis serverkonfiguration](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hur övervakar jag hälsotillstånd och prestanda för Mina cache?
Microsoft Azure Redis-Cache-instanser kan övervakas i den [Azure-portalen](https://portal.azure.com). Du kan visa mått, fästa diagram mått på startsidan, anpassa intervallet för datum och tid för övervakning av diagram, lägga till och ta bort mått från diagrammen och Ställ in aviseringar när vissa villkor är uppfyllda. Mer information finns i [övervakaren Azure Redis-Cache](cache-how-to-monitor.md).

Redis-Cache **resurs menyn** innehåller också flera verktyg för övervakning och felsökning dina cacheminnen.

* **Diagnostisera och lösa problem** innehåller information om vanliga problem och strategier för att lösa dem.
* **Resurshälsa** bevakar resurs och anger om den körs som förväntat. Läs mer om tjänsten för hälsotillstånd Azure-resurshanteraren [Azure Resource health översikt](../resource-health/resource-health-overview.md).
* **Ny supportbegäran** innehåller alternativ för att öppna en supportbegäran för ditt cacheminne.

Dessa verktyg kan du övervaka hälsotillståndet för dina Azure Redis-Cache-instanser och hjälper dig att hantera dina cachelagring program. Mer information finns i avsnittet ”stöd & inställningar för felsökning” i [hur du konfigurerar Azure Redis-Cache](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Varför ser jag tidsgränser
Tidsgränser sker i den klient som du använder för att kommunicera med Redis. När ett kommando som skickas till Redis-servern, kommandot är i kö och Redis-servern hämtar kommandot förr eller senare och kör den. Men klienten kan tidsgräns under den här processen och om det finns ett undantag utlöses på anropande sida. Mer information om felsökning av problem med timeout finns [felsökning på klientsidan](cache-how-to-troubleshoot.md#client-side-troubleshooting) och [StackExchange.Redis tidsgränsfel](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Varför har min klient kopplats från cacheminnet?
Följande är en vanlig orsak till ett cache kopplas från.

* Klientsidans orsaker
  * Klientprogrammet har omdistribueras.
  * Klientprogrammet utföra en åtgärd med skalning.
    * Det kan bero på automatisk skalning när det gäller molntjänster eller Web Apps.
  * Nätverksnivån på klientsidan ändras.
  * Tillfälligt fel uppstod i klienten eller nätverksnoder mellan klienten och servern.
  * Tröskelvärden för bandbredd har uppnåtts.
  * CPU-bundna åtgärder tog för lång tid att slutföra.
* Gör att servern
  * Tjänsten Azure Redis-Cache initieras i standard cachen erbjuder en redundansväxling från den primära noden till den sekundära noden.
  * Azure korrigering instans där cacheminnet har distribuerats
    * Det kan vara för Redis-serveruppdateringar eller allmänt VM underhåll.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Vilket Azure Cache-erbjudande är bäst för mig?
> [!IMPORTANT]
> Enligt förra årets [meddelande](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), Azure Managed Cache Service och cachelagring i Rollinstanser för Azure service **har tagits bort** 30 November 2016. Vår rekommendation är att använda [Azure Redis-Cache](https://azure.microsoft.com/services/cache/). Information om hur du migrerar finns [migrera från Managed Cache Service till Azure Redis-Cache](cache-migrate-to-redis.md).
>
>

### <a name="azure-redis-cache"></a>Azure Redis Cache
Azure Redis-Cache är allmänt tillgänglig i storlekar upp till 53 GB och har en tillgänglighets-SLA för 99,9%. Den nya [premiumnivån](cache-premium-tier-intro.md) erbjuder storlekar upp till 530 GB och stöd för klustring, virtuella nätverk och beständiga med ett SLA för 99,9%.

Azure Redis-Cache ger kunder möjlighet att använda en säker, dedikerad Redis-cache, hanteras av Microsoft. Med det här erbjudandet får du utnyttja kraftfulla funktioner och ekosystem som tillhandahålls av Redis, tillförlitlig värd och övervakning från Microsoft.

Till skillnad från traditionella cacheminnen som endast behandlar nyckel-värdepar är Redis populära för dess hög performant datatyper. Redis också köras atomiska åtgärder på dessa typer, som att lägga till en sträng; öka värdet i en hash; push-installation till en lista. Ange skärningspunkten, union och skillnaden; eller hämta medlemmen med högsta rangordning i en sortering. Andra funktioner som har stöd för transaktioner, pub/sub, Lua skript, nycklar med en begränsad time-to-live, och konfigurationsinställningar för att göra Redis fungerar mer som en traditionell cache.

En annan viktig aspekt till Redis fungerande är uppbyggd kring den felfri, klara öppen källkod ekosystemet. Detta återspeglas i mängd olika Redis-klienter som är tillgänglig över flera språk. Den här ekosystem och ett stort antal klienter kan Azure Redis-Cache som ska användas av nästan alla arbetsbelastningar som du vill skapa i Azure.

Läs mer om att komma igång med Azure Redis-Cache, [så Använd Azure Redis-Cache](cache-dotnet-how-to-use-azure-redis-cache.md) och [dokumentation för Azure Redis-Cache](index.md).

### <a name="managed-cache-service"></a>Hanterad Cache-tjänst
[Managed Cache service har dragits tillbaka 30 November 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Om du vill visa arkiverade dokumentation finns [arkiverade Managed Cache Service dokumentationen](https://msdn.microsoft.com/library/azure/dn386094.aspx).

### <a name="in-role-cache"></a>Cache i roller
[Cachelagring i Rollinstanser har dragits tillbaka 30 November 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Om du vill visa arkiverade dokumentation finns [arkiverade i rollen Cache dokumentationen](https://msdn.microsoft.com/library/azure/dn386103.aspx).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
