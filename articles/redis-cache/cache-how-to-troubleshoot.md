---
title: "Felsökning av Azure Redis-Cache | Microsoft Docs"
description: "Lär dig att lösa vanliga problem med Azure Redis-Cache."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: a88adc300e52c74f2a1fcd2e546ab879000d877e
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Felsökning av Azure Redis-Cache
Den här artikeln innehåller anvisningar för felsökning av följande typer av problem med Azure Redis-Cache.

* [Felsökning på klienten](#client-side-troubleshooting) – det här avsnittet innehåller riktlinjer för att identifiera och lösa problem orsakade av program som ansluter till Azure Redis-Cache.
* [Felsökning på Server](#server-side-troubleshooting) – det här avsnittet innehåller riktlinjer för att identifiera och lösa problem orsakade på serversidan Azure Redis-Cache.
* [StackExchange.Redis tidsgränsfel](#stackexchangeredis-timeout-exceptions) – det här avsnittet innehåller information om felsökning av problem när du använder StackExchange.Redis-klienten.

> [!NOTE]
> Flera åtgärder för felsökning i den här guiden innehåller instruktioner för att köra Redis-kommandon och övervaka olika prestandamått. Mer information och instruktioner finns i artiklarna i den [ytterligare information](#additional-information) avsnitt.
> 
> 

## <a name="client-side-troubleshooting"></a>Felsökning på klienten
Det här avsnittet beskrivs felsöker problem som uppstår på grund av ett villkor i klientprogrammet.

* [Minnesbelastning på klienten](#memory-pressure-on-the-client)
* [Burst av trafik](#burst-of-traffic)
* [Klienten hög CPU-användning](#high-client-cpu-usage)
* [Klienten sida bandbredd överskreds](#client-side-bandwidth-exceeded)
* [Storlek för stora frågor och svar](#large-requestresponse-size)
* [Vad hände med data i Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Minnesbelastning på klienten
#### <a name="problem"></a>Problem
Minnesbelastning på klientdatorn leder till att alla typer av problem med prestanda som kan fördröja behandlingen av data som skickades av Redis-instans utan fördröjning. När minnesbelastning träffar har systemet vanligtvis sidan data från det fysiska minnet på virtuellt minne som finns på disken. Detta *sidan felaktigt* medför att avsevärt långsammare.

#### <a name="measurement"></a>Mätning
1. Övervaka minnesanvändning på datorn för att se till att det inte överstiger tillgängligt minne. 
2. Övervaka den `Page Faults/Sec` prestandaräknare. De flesta datorer ska ha vissa sidfel även under normal drift, så bevaka toppar i den här sidan fel prestandaräknaren som motsvarar timeout.

#### <a name="resolution"></a>Lösning
Uppgradera klienten till en större klient VM-storlek med mer minne eller prova din minne användningsmönster att minska minne consuption.

### <a name="burst-of-traffic"></a>Burst av trafik
#### <a name="problem"></a>Problem
Belastning av trafik i kombination med dålig `ThreadPool` inställningar kan orsaka försening i bearbetning av data som redan har skickats som Redis-servern men har ännu inte används på klientsidan.

#### <a name="measurement"></a>Mätning
Övervaka hur din `ThreadPool` statistik förändras över tid genom att använda code [som detta](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Du kan också titta på den `TimeoutException` meddelande från StackExchange.Redis. Här är ett exempel:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Det finns några problem som är intressanta i meddelandet ovan:

1. Observera att i den `IOCP` avsnitt och `WORKER` avsnitt som du har en `Busy` värde som är större än den `Min` värde. Detta innebär att din `ThreadPool` inställningarna måste du justera.
2. Du kan också se `in: 64221`. Detta anger att 64211 byte har tagits emot i kernel socket lagret men ännu inte har lästs av program (t.ex. StackExchange.Redis). Detta innebär oftast att programmet inte läsa data från nätverket så snabbt som servern skickar den till dig.

#### <a name="resolution"></a>Lösning
Konfigurera din [arbetstråd inställningar](https://gist.github.com/JonCole/e65411214030f0d823cb) se till att din trådpoolen skalar snabbt under burst-scenarier.

### <a name="high-client-cpu-usage"></a>Klienten hög CPU-användning
#### <a name="problem"></a>Problem
Hög CPU-användning på klienten är en indikation på att systemet inte kan hålla med det arbete som den har ombetts att utföra. Det innebär att klienten kan inte bearbeta ett svar från Redis inom rimlig tid även om Redis skickas svaret mycket snabbt.

#### <a name="measurement"></a>Mätning
Övervaka System Wide processoranvändningen via Azure Portal eller associerade prestandaräknaren. Var noga med att inte övervaka *processen* CPU eftersom en enda process kan ha låg CPU-belastning på samma gång övergripande systemet CPU kan vara hög. Håll utkik efter toppar i CPU-användning som motsvarar timeout. På grund av hög CPU, du kan också se högt `in: XXX` värdena i `TimeoutException` felmeddelanden som beskrivs i den [Burst av trafik](#burst-of-traffic) avsnitt.

> [!NOTE]
> StackExchange.Redis 1.1.603 och senare inkluderar den `local-cpu` mått i `TimeoutException` felmeddelanden. Se till att du använder den senaste versionen av den [StackExchange.Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns fel som ständigt fast i koden för att göra den mer robust för timeout så att den senaste versionen är viktigt.
> 
> 

#### <a name="resolution"></a>Lösning
Uppgradera till en större VM-storlek med mer CPU-kapaciteten eller undersöka vad som orsakar processoranvändning. 

### <a name="client-side-bandwidth-exceeded"></a>Klienten sida bandbredd överskreds
#### <a name="problem"></a>Problem
Klientdatorer med olika storlek har begränsningar på hur mycket bandbredd som de finns tillgängliga. Om klienten överskrider den tillgängliga bandbredden, kommer sedan data inte att bearbetas på klientsidan så snabbt som servern skickar den. Detta kan leda till timeout.

#### <a name="measurement"></a>Mätning
Övervaka hur bandbreddsanvändningen förändras över tid genom att använda code [som detta](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Observera att den här koden inte kan köras i vissa miljöer med begränsad behörighet (till exempel Azure webbplatser).

#### <a name="resolution"></a>Lösning
Öka klienten VM-storlek eller minska nätverksbandbredden.

### <a name="large-requestresponse-size"></a>Storlek för stora frågor och svar
#### <a name="problem"></a>Problem
Tidsgränser kan medföra att en stor begäran och svar. Anta exempelvis din timeout-värde konfigureras på din klient är 1 sekund. Ditt program begär två nycklar (t.ex.) ”A” och ”B”) samtidigt (med samma fysiska nätverksanslutningen). De flesta klienter stöder ”Pipelining” begäranden, så att båda begäranden ”A” och ”B” skickas i uppkopplat läge till servern en efter en utan att vänta på svar. Servern skickar svar tillbaka i samma ordning. Om svaret ”A” är stor äta nog den större delen av tidsgräns för efterföljande förfrågningar. 

I följande exempel visar det här scenariot. I det här scenariot begäran ”A” och ”B” skickas snabbt, servern börjar skicka snabbt svar ”A” och ”B”, men på grund av data överföringstiden, ”B” fastna bakom andra begäran och gånger ut trots att servern svarat snabbt.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Mätning
Det är svårt att mäta. Du har i praktiken kan instrumentera din klientkod för att spåra stora begäranden och -svar. 

#### <a name="resolution"></a>Lösning
1. Redis är optimerad för ett stort antal små värden i stället för ett par stora värden. Den bästa lösningen är att dela upp data till relaterade mindre värden. Finns det [vad är perfekt värdeintervallet storlek för redis? Är 100KB för stor? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) efter information kring varför mindre värden rekommenderas.
2. Öka storleken på den virtuella datorn (för klient och Server för Redis-Cache) för att få högre bandbredd, funktioner, vilket minskar data överföringstiden för större svar. Observera att få mer bandbredd på bara servern eller bara på klienten inte kanske är tillräckligt. Mät bandbreddsanvändningen och jämför den med funktionerna i storlek för den virtuella datorn du har för närvarande.
3. Öka antalet `ConnectionMultiplexer` objekt användning och resursallokering begäranden via olika anslutningar.

### <a name="what-happened-to-my-data-in-redis"></a>Vad hände med data i Redis?
#### <a name="problem"></a>Problem
Förväntat för vissa data i min Azure Redis-Cache-instans, men det verkar inte vara det.

#### <a name="resolution"></a>Lösning
Se [vad hände med data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) för möjliga orsaker och lösningar.

## <a name="server-side-troubleshooting"></a>Felsökning på Server
Det här avsnittet beskrivs felsöker problem som uppstår på grund av ett villkor på servern.

* [Minnesbelastning på servern](#memory-pressure-on-the-server)
* [Hög processoranvändning / Server att läsa in](#high-cpu-usage-server-load)
* [Serverns sida bandbredd överskreds](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Minnesbelastning på servern
#### <a name="problem"></a>Problem
Minnesbelastning på serversidan leder till att alla typer av problem med prestanda som kan fördröja behandlingen av begäran. När minnesbelastning träffar har systemet vanligtvis sidan data från det fysiska minnet på virtuellt minne som finns på disken. Detta *sidan felaktigt* medför att avsevärt långsammare. Det finns flera möjliga orsaker till detta minnesbelastning: 

1. Du har fyllt i cacheminnet för att full kapacitet med data. 
2. Redis ser hög minnesfragmenteringen - orsakas normalt genom att lagra stora objekt (Redis är optimerad för en små objekt - finns det [vad är perfekt värdeintervallet storlek för redis? Är 100KB för stor? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) efter mer information). 

#### <a name="measurement"></a>Mätning
Redis visar två mått som kan hjälpa dig att identifiera problemet. Först är `used_memory` och den andra `used_memory_rss`. [De här måtten](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) är tillgängliga i Azure-portalen eller via den [Redis INFO](http://redis.io/commands/info) kommando.

#### <a name="resolution"></a>Lösning
Det finns flera möjliga ändringar som du kan göra för att hålla minnesanvändning felfri:

1. [Konfigurera en princip för minne](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) och Ställ in förfallodatum gånger på dina nycklar. Observera att det räcker kanske inte om du har fragmentering.
2. [Anger värdet maxmemory-reserverade](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) som är tillräckligt stor för att kompensera för minnesfragmentering.
3. Dela upp stora cachelagrade objekten i mindre relaterade objekt.
4. [Skala](cache-how-to-scale.md) till en större cachestorlek.
5. Om du använder en [premium-cache med Redis cluster aktiverat](cache-how-to-premium-clustering.md) kan du [öka antalet shards](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Hög processoranvändning / Server att läsa in
#### <a name="problem"></a>Problem
Hög CPU-användning kan leda till att klienten kan inte bearbeta ett svar från Redis inom rimlig tid även om Redis skickas svaret mycket snabbt.

#### <a name="measurement"></a>Mätning
Övervaka System Wide processoranvändningen via Azure Portal eller associerade prestandaräknaren. Var noga med att inte övervaka *processen* CPU eftersom en enda process kan ha låg CPU-belastning på samma gång övergripande systemet CPU kan vara hög. Håll utkik efter toppar i CPU-användning som motsvarar timeout.

#### <a name="resolution"></a>Lösning
[Skala](cache-how-to-scale.md) till en större cache tjänstnivån med mer CPU-kapaciteten eller undersöka vad som orsakar processoranvändning. 

### <a name="server-side-bandwidth-exceeded"></a>Serverns sida bandbredd överskreds
#### <a name="problem"></a>Problem
Cacheinstanser med olika storlek har begränsningar på hur mycket bandbredd som de finns tillgängliga. Om servern överskrider den tillgängliga bandbredden, kommer data inte skickas till klienten som snabbt. Detta kan leda till timeout.

#### <a name="measurement"></a>Mätning
Du kan övervaka den `Cache Read` mått som avser mängden data har lästs från cacheminnet i megabyte per sekund (MB/s) under det angivna intervallet för rapportering. Det här värdet motsvarar nätverksbandbredden som används av det här cacheminnet. Om du vill konfigurera aviseringar för server side nätverket bandbreddsgränser, kan du skapa dem med hjälp av det här `Cache Read` räknaren. Jämför din avläsningar med värdena i [tabellen](cache-faq.md#cache-performance) för observerade bandbreddsgränser för olika cache priser nivåer och storlekar.

#### <a name="resolution"></a>Lösning
Om du är konsekvent nära observerade Maximal bandbredd för storleken på din prisnivå nivå och cache kan du överväga att [skalning](cache-how-to-scale.md) till en prisnivå nivå eller storlek som har större nätverksbandbredd med värdena i [tabellen](cache-faq.md#cache-performance)som en vägledning.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis tidsgränsfel
StackExchange.Redis använder en konfigurationsinställning namngivna `synctimeout` för synkrona åtgärder som har ett standardvärde på 1 000 ms. Om ett synkrona anrop inte slutförs inom den angivna tiden genererar StackExchange.Redis klienten ett timeout-fel som liknar följande exempel.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Det här felmeddelandet innehåller mått som kan hjälpa dig att peka orsaken och möjlig lösning av problemet. Följande tabell innehåller information om fel meddelande mått.

| Fel meddelande mått | Information |
| --- | --- |
| PL |I det senaste tidsintervallet: 0-kommandon som har utfärdats. |
| hanterare av |Hanteraren för ingång utförs `socket.select` så att den frågar OS att indikera en socket som har att göra; i princip: läsaren inte aktivt lästes från nätverket eftersom det inte tror att det finns ingenting att göra |
| Kön |Totalt antal åtgärder pågående har 73 |
| qu |6 pågående åtgärder i ej skickade kön och ännu inte har skrivits till nätverkets utgående |
| qs |67 han pågående åtgärder har skickats till servern, men svaret är inte tillgänglig ännu. Svaret kan vara `Not yet sent by the server` eller`sent by the server but not yet processed by the client.` |
| qc |0 av pågående åtgärder har sett svar men ännu inte har markerats som slutförd på grund av att vänta på slutförande loop |
| wr |Det finns en aktiv skrivaren (d.v.s. 6 ej skickade begäranden inte ignoreras) byte/activewriters |
| i |Det finns ingen aktiv läsare och noll byte är tillgängliga för att läsa på NIC byte/activereaders |

### <a name="steps-to-investigate"></a>Steg för att undersöka
1. Som bästa praxis se till att använder du följande mönster för att ansluta när du använder StackExchange.Redis-klienten.

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
    ````

    Mer information finns i [ansluta till cacheminnet med StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Se till att din Azure Redis-Cache och klientprogrammet finns i samma region i Azure. Till exempel du kanske att få timeout när ditt cacheminne i östra USA men klienten finns i USA, västra och begäran inte slutförs inom den `synctimeout` intervall eller så kan du får ett timeout när du felsöker från lokala utvecklingsdatorn. 
   
    Det har bör ha cacheminnet och klienten i samma Azure-region. Om du har ett scenario som innehåller mellan region anrop, bör du ange den `synctimeout` intervall för ett högre värde än standardvärdet 1 000 ms-intervall genom att inkludera en `synctimeout` egenskap i anslutningssträngen. I följande exempel visas en StackExchange.Redis cache anslutning sträng fragment med en `synctimeout` av 2000 ms.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Se till att du använder den senaste versionen av den [StackExchange.Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns fel som ständigt fast i koden för att göra den mer robust för timeout så att den senaste versionen är viktigt.
3. Om det finns begäranden som är komma bundna av bandbreddsbegränsningar på servern eller klienten, kan det ta längre tid att slutföra och därmed orsakar timeout. Om det finns en tidsgräns på grund av nätverkets bandbredd på servern, se [Server side bandbredd överskridit](#server-side-bandwidth-exceeded). Om det finns en tidsgräns på grund av nätverksbandbredd för klienten, se [klienten sida bandbredd överskridit](#client-side-bandwidth-exceeded).
4. Är du komma CPU bundna på servern eller på klienten?
   
   * Kontrollera om du är komma bundna av processor på klienten vilket kan leda till begäran om att inte behandlas inom den `synctimeout` intervall, vilket kan orsaka en tidsgräns. Flytta till en större klienten eller distribuerar belastningen kan hjälpa till att styra. 
   * Kontrollera om du får CPU bunden på servern genom att övervaka den `CPU` [cachelagra prestanda mått](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Begäranden som kommer medan Redis CPU-bundna kan orsaka dessa begäranden för att timeout. För att lösa det kan du fördela belastningen över flera delar i premium-cache eller uppgradera till en större storlek eller prisnivå. Mer information finns i [servern sida bandbredd överskridit](#server-side-bandwidth-exceeded).
5. Finns det kommandon som tar lång tid att bearbeta på servern? Tidskrävande kommandon som tar lång tid att bearbeta på redis-servern kan orsaka timeout. Är några exempel på tidskrävande kommandon `mget` med ett stort antal nycklar, `keys *` eller felaktiga lua skript. Du kan ansluta till din Azure Redis-Cache-instans med hjälp av redis-cli-klienten eller använda den [Redis-konsolen](cache-configure.md#redis-console) och kör den [SlowLog](http://redis.io/commands/slowlog) kommando för att se om det finns begäranden tar längre tid än förväntat. Redis-servern och StackExchange.Redis är optimerad för många små begäranden i stället för färre stora begäranden. Dela upp dina data i mindre segment kan du förbättra här saker. 
   
    Information om hur du ansluter till Azure Redis-Cache SSL-slutpunkten med redis-cli och stunnel finns i [om ASP.NET Sessionstillståndsprovider för Redis-förhandsversionen](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogginlägg. Mer information finns i [SlowLog](http://redis.io/commands/slowlog).
6. Hög Redis-serverbelastning kan orsaka timeout. Du kan övervaka serverbelastningen genom att övervaka den `Redis Server Load` [cachelagra prestanda mått](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). En serverbelastningen 100 (högsta värde) innebär att redis-servern har varit upptagen med ingen inaktivitetstid bearbetning av begäranden. Om du vill se om vissa begäranden tar upp alla funktioner för server, kör du kommandot SlowLog enligt beskrivningen i föregående stycke. Mer information finns i [hög processoranvändning / Server att läsa in](#high-cpu-usage-server-load).
7. Var det andra händelser på klientsidan som kan ha orsakat ett nätverk blip? Kontrollera på klienten (webb, arbetsrollen eller en Iaas VM) om det fanns en händelse som skala antalet instanser av klient upp eller ned eller distribuerar en ny version av klienten eller Autoskala aktiveras? I våra tester som vi har hittat att Autoskala eller skala upp/ned kan utgående nätverksanslutningen gå förlorade i några sekunder. StackExchange.Redis koden är känsligt för sådana händelser och återupprättas. Under den här tiden på ny anslutning kan alla förfrågningar i kön timeout.
8. Har det skett en stor begäran föregående flera små begäran till Redis-Cache som gjort timeout? Parametern `qs` i felet meddelandet anger hur många begäranden har skickats från klienten till servern, men har ännu inte bearbetat svaret. Det här värdet kan hålla växer eftersom StackExchange.Redis använder en TCP-anslutning och kan endast läsa ett svar i taget. Även om den första åtgärden tidsgränsen avbryts inte de data som skickas till och från servern och andra begäranden blockeras tills det är klart, orsakar timeout. En lösning är att minimera risken för timeout genom att säkerställa att din cache är tillräckligt stor för din arbetsbelastning och dela upp stora värden i mindre segment. En annan möjlig lösning är att använda en pool av `ConnectionMultiplexer` objekt i din klient och Välj minst inlästa `ConnectionMultiplexer` när du skickar en ny begäran. Detta bör förhindra att en enda tidsgräns orsakar övriga förfrågningar till också timeout.
9. Om du använder `RedisSessionStateprovider`, se till att du har angett korrekt gör timeout. `retrytimeoutInMilliseconds`bör vara högre än `operationTimeoutinMilliseonds`, annars inga nya försök utförs. I följande exempel `retrytimeoutInMilliseconds` är inställd på 3000. Mer information finns i [ASP.NET-Sessionstillståndsprovider för Azure Redis-Cache](cache-aspnet-session-state-provider.md) och [hur du använder konfigurationsparametrarna för Sessionstillståndsprovider och Utdatacacheprovider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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


1. Kontrollera minnesanvändning på Azure Redis-Cache-servern genom att [övervakning](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` och `Used Memory`. Om en princip för borttagning är på plats, Redis börjar ta bort nycklar när `Used_Memory` når cachestorleken. Vi rekommenderar `Used Memory RSS` ska bara vara något högre än `Used memory`. En stor skillnad innebär att det minnesfragmenteringen (interna eller externa. När `Used Memory RSS` är mindre än `Used Memory`, innebär det en del av cache-minnet har bytts av operativsystemet. Om detta inträffar kan du förväntar dig några betydande fördröjningar. Eftersom Redis inte har kontroll över hur dess allokeringar mappas till minnessidor som hög `Used Memory RSS` beror ofta på en topp i minnesanvändning. När Redis Frigör minne, minnet ges tillbaka till allokeraren och allokeraren kanske eller kanske inte ger minnet tillbaka till systemet. Det kan finnas en avvikelse mellan det `Used Memory` förbrukning av värdet och minne som rapporteras av operativsystemet. Du kanske på grund av faktumet minne används och publicerat som Redis, men inte angivna tillbaka till systemet. Du kan utföra följande steg för att minimera minnesproblem.
   
   * Uppgradera cachen till en större storlek så att du inte kör visa begränsningar i minnet i systemet.
   * Ange förfallodatum gånger på nycklarna så att äldre värden har avlägsnats proaktivt.
   * Övervakare av den `used_memory_rss` cachelagra mått. När det här värdet närmar sig sin cachens storlek, troligen kommer att starta ser prestandaproblem. Distribuera data över flera delar om du använder en premium-cache, eller uppgradera till en större cachestorlek.
   
   Mer information finns i [minnesbelastning på servern](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Ytterligare information
* [Vilket erbjudande och vilken storlek ska jag använda för Redis-cache?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [Hur kan mäta och testa prestanda för Mina cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Hur kör Redis kommandon?](cache-faq.md#how-can-i-run-redis-commands)
* [Hur du övervakar Azure Redis-Cache](cache-how-to-monitor.md)

