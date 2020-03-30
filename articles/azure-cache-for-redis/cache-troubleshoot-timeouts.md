---
title: Felsöka överskridna tidsgränser för Azure Cache for Redis
description: Lär dig hur du löser vanliga timeout-problem med Azure Cache för Redis, till exempel redis-serverkorrigering och StackExchange.Redis timeout-undantag.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4b8cfed883ffef780de2e82e3f309e97bcb5515c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278251"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Felsöka överskridna tidsgränser för Azure Cache for Redis

I det här avsnittet beskrivs felsökning av timeout-problem som uppstår vid anslutning till Azure Cache för Redis.

- [Korrigering av Redis-server](#redis-server-patching)
- [Undantag för StackExchange.Redis-timeout](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Flera av felsökningsstegen i den här guiden innehåller instruktioner för att köra Redis-kommandon och övervaka olika prestandamått. Mer information och instruktioner finns i artiklarna i avsnittet [Ytterligare information.](#additional-information)
>

## <a name="redis-server-patching"></a>Korrigering av Redis-server

Azure Cache för Redis uppdaterar regelbundet sin serverprogramvara som en del av den hanterade tjänstfunktionen som den tillhandahåller. Denna [lappaktivitet](cache-failover.md) sker till stor del bakom scenen. Under redundans när Redis-servernoder korrigeras kan Redis-klienter som är anslutna till dessa noder uppleva tillfälliga tidsutgångar när anslutningar växlas mellan dessa noder. Se [Hur påverkar en redundans min klientprogram](cache-failover.md#how-does-a-failover-affect-my-client-application) för mer information om vilka biverkningar som kan ha på ditt program och hur du kan förbättra hanteringen av korrigeringshändelser.

## <a name="stackexchangeredis-timeout-exceptions"></a>Undantag för StackExchange.Redis-timeout

StackExchange.Redis använder en `synctimeout` konfigurationsinställning som namnges för synkronåtgärder med ett standardvärde på 1000 ms. Om ett synkront anrop inte slutförs under den här tiden genererar StackExchange.Redis-klienten ett timeout-fel som liknar följande exempel:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Det här felmeddelandet innehåller mått som kan hjälpa dig att peka på orsaken och möjliga lösningen av problemet. Följande tabell innehåller information om måtten för felmeddelanden.

| Mått på felmeddelande | Information |
| --- | --- |
| inst |I den senaste gången segmentet: 0 kommandon har utfärdats |
| Mgr |Socket manager gör `socket.select`, vilket innebär att det ber OS att ange ett uttag som har något att göra. Läsaren är inte aktivt läsa från nätverket eftersom det inte tror att det finns något att göra |
| kö |Det finns totalt 73 pågående operationer |
| Qu |6 av de pågående åtgärderna finns i den oskickade kön och har ännu inte skrivits till det utgående nätverket |
| Qs |67 av de pågående åtgärderna har skickats till servern, men ett svar är ännu inte tillgängligt. Svaret kan `Not yet sent by the server` vara eller`sent by the server but not yet processed by the client.` |
| Qc |0 av de pågående åtgärderna har sett svar men har ännu inte markerats som fullständiga eftersom de väntar på slutförandeslingan |
| Wr |Det finns en aktiv författare (vilket innebär att de 6 begäranden som inte har ignorerats) byte/activewriters |
| in |Det finns inga aktiva läsare och noll byte finns tillgängliga för att läsas på NIC-byte/activereaders |

Du kan använda följande steg för att undersöka möjliga grundorsaker.

1. Se till att du använder följande mönster för att ansluta när du använder StackExchange.Redis-klienten.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Mer information finns i [Ansluta till cacheminnet med StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Kontrollera att servern och klientprogrammet finns i samma region i Azure. Du kan till exempel få timeout när cacheminnet finns i östra USA, men klienten `synctimeout` är i västra USA och begäran slutförs inte inom intervallet eller så får du timeout när du felsöker från din lokala utvecklingsdator. 

    Vi rekommenderar starkt att du har cacheminnet och i klienten i samma Azure-region. Om du har ett scenario som innehåller anrop mellan regioner bör du ange `synctimeout` intervallet till ett värde `synctimeout` som är högre än standardintervallet på 1 000 ms genom att inkludera en egenskap i anslutningssträngen. I följande exempel visas ett utdrag av en anslutningssträng för StackExchange.Redis `synctimeout` från Azure Cache for Redis med en av 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Se till att du använder den senaste versionen av [Paketet StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar som ständigt fixas i koden för att göra det mer robust till timeouts så att ha den senaste versionen är viktigt.
1. Om dina begäranden är bundna av bandbreddsbegränsningar på servern eller klienten tar det längre tid för dem att slutföra och kan orsaka timeout. Information om du vill se om timeouten beror på nätverksbandbredd på servern finns i [bandbreddsbegränsning på serversidan](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Information om hur du ser om timeouten beror på bandbredden för klientnätverket finns i [bandbreddsbegränsning på klientsidan](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Får du CPU-bunden på servern eller på klienten?

   - Kontrollera om du blir bunden av CPU på din klient. Hög CPU kan orsaka att begäran inte `synctimeout` bearbetas inom intervallet och orsaka en begäran att time out. Om du flyttar till en större klientstorlek eller distribuerar belastningen kan du kontrollera problemet.
   - Kontrollera om du får CPU-bunden på servern genom att övervaka [prestandamåttet för CPU-cachen](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Begäranden som kommer in medan Redis är CPU-bunden kan orsaka att dessa begäranden time out. Om du vill åtgärda det här villkoret kan du distribuera belastningen över flera shards i en premiumcache eller uppgradera till en större storlek eller prisnivå. Mer information finns i [Bandbreddsbegränsning på serversidan](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Finns det kommandon som tar lång tid att bearbeta på servern? Långvariga kommandon som tar lång tid att bearbeta på redis-servern kan orsaka timeout. Mer information om långvariga kommandon finns i [Kommandon med lång drift](cache-troubleshoot-server.md#long-running-commands). Du kan ansluta till Azure Cache för Redis-instans med redis-cli-klienten eller [Redis-konsolen](cache-configure.md#redis-console). Kör sedan [kommandot SLOWLOG](https://redis.io/commands/slowlog) för att se om det finns begäranden långsammare än förväntat. Redis Server och StackExchange.Redis är optimerade för många små begäranden i stället för färre stora begäranden. Att dela upp dina data i mindre bitar kan förbättra saker här.

    Information om hur du ansluter till cachens SSL-slutpunkt med redis-cli och stunnel finns i blogginlägget [Announcing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Hög Redis-serverbelastning kan orsaka timeout. Du kan övervaka serverbelastningen `Redis Server Load` genom att övervaka [cacheprestandamåttet](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). En serverbelastning på 100 (maximalt värde) innebär att redis-servern har varit upptagen, utan inaktiv tid, bearbetningsbegäranden. Om du vill se om vissa begäranden tar upp alla serverfunktioner kör du kommandot SlowLog enligt beskrivningen i föregående stycke. Mer information finns i Hög CPU-användning / Server Load.
1. Fanns det någon annan händelse på klientsidan som kunde ha orsakat ett nätverk blip? Vanliga händelser är: skala antalet klientinstanser uppåt eller nedåt, distribuera en ny version av klienten eller aktiverad automatisk skalning. I våra tester har vi funnit att automatisk skalning eller skalning upp / ned kan orsaka utgående nätverksanslutning att gå förlorade i flera sekunder. StackExchange.Redis-koden är motståndskraftig mot sådana händelser och återansluter. När du ansluter igen kan alla begäranden i kön ta time out.
1. Fanns det en stor begäran som föregick flera små begäranden till cachen som timelade ut? Parametern `qs` i felmeddelandet visar hur många begäranden som skickades från klienten till servern, men har inte bearbetat något svar. Det här värdet kan fortsätta växa eftersom StackExchange.Redis använder en enda TCP-anslutning och bara kan läsa ett svar i taget. Även om den första åtgärden tog time out, hindrar det inte mer data från att skickas till eller från servern. Andra begäranden blockeras tills den stora begäran är klar och kan orsaka time outs. En lösning är att minimera risken för timeout genom att se till att cachen är tillräckligt stor för din arbetsbelastning och dela upp stora värden i mindre segment. En annan möjlig lösning är `ConnectionMultiplexer` att använda en pool med `ConnectionMultiplexer` objekt i klienten och välja den minst inlästa när du skickar en ny begäran. Inläsning över flera anslutningsobjekt bör förhindra att en enda timeout orsakar att andra begäranden också kan ta timeout.
1. Om du använder `RedisSessionStateProvider`kontrollerar du att du har ställt in tidsgränsen för återförsök korrekt. `retryTimeoutInMilliseconds`högre än `operationTimeoutInMilliseconds`, annars sker inga återförsök. I följande `retryTimeoutInMilliseconds` exempel anges till 3000. Mer information finns [i ASP.NET Sessionstillståndsprovider för Azure Cache för Redis](cache-aspnet-session-state-provider.md) och [hur du använder konfigurationsparametrarna för Sessionstillståndsprovidern och leverantör av utdatacache.](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Kontrollera minnesanvändningen på Azure Cache for `Used Memory`Redis-servern genom att [övervaka](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` och . Om det finns en vräkningsprincip börjar Redis `Used_Memory` ta bort nycklar när cachestorleken nås. Helst `Used Memory RSS` bör vara bara något `Used memory`högre än . En stor skillnad innebär att det finns minnesfragmentering (intern eller extern). När `Used Memory RSS` är `Used Memory`mindre än betyder det att en del av cacheminnet har bytts ut av operativsystemet. Om detta byte sker, kan du förvänta dig några betydande latenser. Eftersom Redis inte har kontroll över hur dess allokeringar `Used Memory RSS` mappas till minnessidor är hög ofta resultatet av en ökning av minnesanvändningen. När Redis-servern frigör minne tar allokeraren minnet, men det kan eller inte kan ge minnet tillbaka till systemet. Det kan finnas en `Used Memory` diskrepans mellan värdet och minnesförbrukningen som rapporterats av operativsystemet. Minnet kan ha använts och släppts av Redis men inte getts tillbaka till systemet. Du kan minska minnesproblemen:

   - Uppgradera cacheminnet till en större storlek så att du inte körs mot minnesbegränsningar på systemet.
   - Ange förfallotider på nycklarna så att äldre värden ska vräkas proaktivt.
   - Övervaka `used_memory_rss` cachemåttet. När det här värdet närmar sig storleken på deras cache, är det troligt att du börjar se prestandaproblem. Distribuera data över flera shards om du använder en premiumcache eller uppgradera till en större cachestorlek.

   Mer information finns i [Minnestryck på Redis-servern](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Ytterligare information

- [Felsöka problem på Azure Cache for Redis-klientsidan](cache-troubleshoot-client.md)
- [Felsöka problem på Azure Cache for Redis-serversidan](cache-troubleshoot-server.md)
- [Hur kan jag jämföra och testa prestanda för min cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Övervaka Azure Cache för Redis](cache-how-to-monitor.md)
