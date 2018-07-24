---
title: Så här felsöker du Azure Redis Cache | Microsoft Docs
description: Lär dig att lösa vanliga problem med Azure Redis Cache.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: b41fc5c41b2e0d1e5d5ba3e39c7f6063cf57c6c2
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205790"
---
# <a name="how-to-troubleshoot-azure-redis-cache"></a>Så här felsöker du Azure Redis Cache
Den här artikeln innehåller riktlinjer för felsökning av följande typer av problem med Azure Redis Cache.

* [Felsökning av klientsidan](#client-side-troubleshooting) – det här avsnittet innehåller riktlinjer för att identifiera och lösa problem orsakade av program som ansluter till Azure Redis Cache.
* [Felsökning av serversidan](#server-side-troubleshooting) – det här avsnittet innehåller riktlinjer för att identifiera och lösa problem orsakade på serversidan för Azure Redis Cache.
* [Tidsgränsundantag i StackExchange.Redis](#stackexchangeredis-timeout-exceptions) – det här avsnittet innehåller information om hur du felsöker problem när du använder StackExchange.Redis-klienten.

> [!NOTE]
> Flera av anvisningarna i den här guiden innehåller anvisningar för att köra Redis-kommandon och övervaka olika prestandamått. Mer information och anvisningar finns i artiklarna i den [ytterligare information](#additional-information) avsnittet.
> 
> 

## <a name="client-side-troubleshooting"></a>Felsökning av klientsidan
Det här avsnittet beskrivs felsökning av problem som uppstår på grund av ett villkor i klientprogrammet.

* [Minnesbelastning på klienten](#memory-pressure-on-the-client)
* [Trafikökningen](#burst-of-traffic)
* [Klienten hög CPU-användning](#high-client-cpu-usage)
* [Klientsidan bandbredd har överskridits](#client-side-bandwidth-exceeded)
* [Stora begäranden/svar](#large-requestresponse-size)
* [Vad hände med mina data i Redis?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Minnesbelastning på klienten
#### <a name="problem"></a>Problem
Minnesbelastning på klientdatorn leder till alla typer av problem med prestanda som kan fördröja behandlingen av data som skickades av Redis-instans utan fördröjning. När minnesbelastning når har systemet vanligtvis sidan data från det fysiska minnet till det virtuella minnet som finns på disk. Detta *sidan felaktig* systemet betydligt långsammare.

#### <a name="measurement"></a>Mått
1. Övervaka användning av minne på datorn för att se till att det inte överstiger tillgängligt minne. 
2. Övervaka den `Page Faults/Sec` prestandaräknaren. De flesta system har vissa sidfel även under normal drift, så Håll utkik efter toppar i den här sidan fel prestandaräknare, vilket kan ha orsakat timeout.

#### <a name="resolution"></a>Lösning
Uppgradera din klient till en större klient VM-storlek med mer minne eller gå djupare in dina användningsmönster för minne för att minska minnesanvändningen.

### <a name="burst-of-traffic"></a>Trafikökningen
#### <a name="problem"></a>Problem
Trafiktoppar kombineras med dåligt `ThreadPool` inställningar kan orsaka försening i bearbetning av data som redan har skickat av Redis-servern men har ännu inte används på klientsidan.

#### <a name="measurement"></a>Mått
Övervaka hur din `ThreadPool` statistik ändras över tid med hjälp av kod [så här](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Du kan också titta på den `TimeoutException` meddelande från StackExchange.Redis. Här är ett exempel:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Det finns flera problem som är intressanta i föregående meddelande:

1. Observera att i den `IOCP` avsnittet och `WORKER` avsnitt som du har en `Busy` värde som är större än den `Min` värde. Detta innebär att din `ThreadPool` inställningar behöver justeras.
2. Du kan också se `in: 64221`. Det här värdet anger att 64,211 byte har tagits emot i kernel socket layer men ännu inte har lästs av programmet (till exempel StackExchange.Redis). Detta innebär normalt att ditt program inte läsa data från nätverket så snabbt som servern skickar det till dig.

#### <a name="resolution"></a>Lösning
Konfigurera din [arbetstråd inställningar](https://gist.github.com/JonCole/e65411214030f0d823cb) mycket kraft för att se till att din trådpoolen skalar upp snabbt under scenarier.

### <a name="high-client-cpu-usage"></a>Klienten hög CPU-användning
#### <a name="problem"></a>Problem
Hög processoranvändning på klienten är en indikation på att systemet inte kan hålla med det arbete som har blivit ombedd att utföra. Detta innebär att klienten kan inte bearbeta ett svar från Redis i tid, även om svaret skickas snabbt i Redis.

#### <a name="measurement"></a>Mått
Övervaka systemet Wide CPU-användningen via Azure Portal eller tillhörande prestandaräknaren. Var noga med att inte övervaka *processen* CPU eftersom en enda process kan ha låg CPU-belastning på samma tid som hela systemet CPU kan vara hög. Titta på för processoranvändningen toppar som kan ha orsakat timeout. Till följd av hög CPU, du kan också se hög `in: XXX` värdena i `TimeoutException` felmeddelanden som beskrivs i den [trafikökningen](#burst-of-traffic) avsnittet.

> [!NOTE]
> StackExchange.Redis 1.1.603 och senare inkluderar den `local-cpu` mått i `TimeoutException` felmeddelanden. Se till att du använder den senaste versionen av den [StackExchange.Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar ständigt som i koden för att göra den mer robust för tidsgränser, så det är viktigt att ha den senaste versionen.
> 
> 

#### <a name="resolution"></a>Lösning
Uppgradera till en större VM-storlek med mer CPU-kapacitet eller undersöka vad som orsakar processoranvändning. 

### <a name="client-side-bandwidth-exceeded"></a>Klientsidan bandbredd har överskridits
#### <a name="problem"></a>Problem
Beroende på arkitekturen för klientdatorer, de kan ha begränsningar på hur mycket nätverksbandbredd som de har tillgängliga. Om klienten överskrider den tillgängliga bandbredden genom att överbelasta nätverkskapacitet, har sedan data inte bearbetats på klientsidan så snabbt som servern skickar den. Den här situationen kan leda till tidsgränser.

#### <a name="measurement"></a>Mått
Övervaka hur din bandbreddsanvändning ändras med tiden med hjälp av kod [så här](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Den här koden kan inte köras i vissa miljöer med begränsad behörighet (t.ex. Azure-webbplatser).

#### <a name="resolution"></a>Lösning
Öka klienten VM-storlek eller minska nätverksbandbredden.

### <a name="large-requestresponse-size"></a>Stora begäranden/svar
#### <a name="problem"></a>Problem
En stor begäran/svar kan orsaka timeout. Anta till exempel din timeoutvärdet som konfigurerats på klienten är 1 sekund. Ditt program begär två nycklar (till exempel ”A” och ”B”) på samma gång (med samma fysiska nätverksanslutning). De flesta klienter stöder ”Pipelining” begäranden, så att båda förfrågningarna ”A” och ”B” skickas för anslutningen till servern en efter en utan att behöva vänta på svar. Servern skickar svar tillbaka i samma ordning. Om svaret ”A” är tillräckligt stor för äta den mesta av tidsgränsen för efterföljande förfrågningar. 

I följande exempel visar det här scenariot. I det här scenariot begäran ”A” och ”B” skickas snabbt, servern börjar skicka svar ”A” och ”B” snabbt men på grund av tider för överföring av data, ”B” fastnar bakom andra begäran och tidsgränsen ut trots att servern svarat snabbt.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Mått
Den här begäran/svar är svåra att mäta. Du har i princip att instrumentera klientkoden för att spåra stora begäranden och svar. 

#### <a name="resolution"></a>Lösning
1. Redis är optimerad för ett stort antal små värden snarare än några stora värden. Det en bättre lösningen är att dela upp data till relaterade lägre värden. Se den [vad är perfekt värdeintervallet storlek för redis? Är 100 KB för stor? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) efter mer information om varför lägre värden rekommenderas.
2. Öka storleken på den virtuella datorn (för klienten och Redis Cache-Server) för att få högre bandbredd-funktioner, vilket minskar tider för överföring av data för större svar. Få mer bandbredd på bara servern eller bara på kanske klienten inte är tillräckligt. Mät bandbreddsanvändningen och jämför den med funktionerna i storleken på virtuell dator som du har för närvarande.
3. Öka antalet `ConnectionMultiplexer` objekt användning och resursallokering begäranden över olika anslutningar.

### <a name="what-happened-to-my-data-in-redis"></a>Vad hände med mina data i Redis?
#### <a name="problem"></a>Problem
Jag hade förväntat för vissa data kan på min Azure Redis Cache-instans, men det verkar inte vara det.

#### <a name="resolution"></a>Lösning
Se [vad hände med mina data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) för möjliga orsaker och lösningar.

## <a name="server-side-troubleshooting"></a>Felsökning på Server
Det här avsnittet beskrivs felsökning av problem som uppstår på grund av ett villkor på servern.

* [Minnesbelastning på servern](#memory-pressure-on-the-server)
* [Hög CPU-användning / serverbelastningen](#high-cpu-usage-server-load)
* [Serverns sida bandbredd har överskridits](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Minnesbelastning på servern
#### <a name="problem"></a>Problem
Minnesbelastning på serversidan leder till alla typer av problem med prestanda som kan fördröja behandlingen av förfrågningar. När minnesbelastning når har systemet vanligtvis sidan data från det fysiska minnet till det virtuella minnet som finns på disk. Detta *sidan felaktig* systemet betydligt långsammare. Det finns flera möjliga orsaker till det här minnesbelastning: 

1. Du har fyllt cacheminnet till fullständig kapacitet med data. 
2. Redis ser hög minnesfragmentering - oftast på grund av att lagra stora objekt (Redis är optimerad för en liten objekt – Se den [vad är perfekt värdeintervallet storlek för redis? Är 100 KB för stor? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) publicera information). 

#### <a name="measurement"></a>Mått
Redis visar två mått som kan hjälpa dig identifiera problemet. Först är `used_memory` och den andra är `used_memory_rss`. [De här måtten](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) är tillgängliga i Azure Portal eller via den [Redis INFO](http://redis.io/commands/info) kommando.

#### <a name="resolution"></a>Lösning
Det finns flera möjliga ändringar som du kan göra för att hålla minnesanvändning felfri:

1. [Konfigurera en princip för minne](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) och ange förfallodatum tider på dina nycklar. Den här konfigurationen kanske inte är tillräckligt om du har fragmentering.
2. [Anger värdet maxmemory-reserverade](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) som är tillräckligt stor för att kompensera för minnesfragmentering.
3. Dela upp dina stora cachelagrade objekt till mindre relaterade objekt.
4. [Skala](cache-how-to-scale.md) till en större cachestorlek.
5. Om du använder en [premium-cache med Redis-kluster som är aktiverad](cache-how-to-premium-clustering.md), kan du [öka antalet shards](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Hög CPU-användning / serverbelastningen
#### <a name="problem"></a>Problem
Hög CPU-användning kan innebära att klienten kan inte bearbeta ett svar från Redis i tid, även om svaret skickas snabbt i Redis.

#### <a name="measurement"></a>Mått
Övervaka systemet Wide CPU-användningen via Azure Portal eller tillhörande prestandaräknaren. Var noga med att inte övervaka *processen* CPU eftersom en enda process kan ha låg CPU-belastning på samma tid som hela systemet CPU kan vara hög. Titta på för processoranvändningen toppar som kan ha orsakat timeout.

#### <a name="resolution"></a>Lösning
* Granska alla råd och aviseringar som nämns i den [Redis Cache Advisor](cache-configure.md#redis-cache-advisor).
* Granska även de andra rekommendationerna i det här avsnittet och [Metodtips för Azure Redis](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f) att se om du har skyddat alla alternativ för att ytterligare optimera din cache och din klient. 
* Granska den [prestanda för Azure Redis Cache](cache-faq.md#azure-redis-cache-performance) diagram och se om du nära övre tröskelvärden för den aktuella nivån. Om det behövs [skala](cache-how-to-scale.md) till en större cache-nivå med mer CPU-kapacitet. Om du använder Premium-nivån redan kan du vilja [skala ut med klustring](cache-how-to-premium-clustering.md)


### <a name="server-side-bandwidth-exceeded"></a>Serverns sida bandbredd har överskridits
#### <a name="problem"></a>Problem
Beroende på storleken på cache-instanser, de kan ha begränsningar på hur mycket nätverksbandbredd som de har tillgängliga. Om servern överskrider den tillgängliga bandbredden, skickas inte data till klienten så snart. Den här situationen kan leda till tidsgränser.

#### <a name="measurement"></a>Mått
Du kan övervaka den `Cache Read` mått, som avser mängden data har lästs från cacheminnet i megabyte per sekund (MBIT/s) under det angivna intervallet för rapportering. Det här värdet motsvarar nätverksbandbredden som används av det här cacheminnet. Om du vill ställa in aviseringar för serversidan nätverk bandbreddsgränser, kan du skapa dem med hjälp av detta `Cache Read` räknaren. Jämför din information med värdena i [den här tabellen](cache-faq.md#cache-performance) för observerade bandbreddsgränserna för olika cache priser nivåer och storlekar.

#### <a name="resolution"></a>Lösning
Om du är konsekvent nära observerade Maximal bandbredd för din prisnivå nivå och cache-storlek kan du överväga att [skalning](cache-how-to-scale.md) till en prisnivå nivå eller storlek som har större bandbredd i nätverket med hjälp av värdena i [den här tabellen](cache-faq.md#cache-performance)som vägledning.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis timeout-undantag
StackExchange.Redis använder en konfigurationsinställning namngivna `synctimeout` för synkrona åtgärder som har ett standardvärde på 1 000 ms. Om ett synkron anrop inte slutförs inom den stipulerade tiden, utlöser StackExchange.Redis-klienten ett timeout-fel som liknar följande exempel:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Det här felmeddelandet innehåller mått som kan hjälpa till att peka orsaken och möjlig lösning av problemet. I följande tabell innehåller information om felmått för meddelandet.

| Fel meddelande mått | Information |
| --- | --- |
| inst |Under det senaste tidsintervallet: 0-kommandon som har utfärdats |
| hanteraren |Socket manager utför `socket.select`, vilket innebär att den ber operativsystem och ange en socket som har att göra; i princip: läsaren inte aktivt lästes från nätverket eftersom det inte tror att det finns något att göra |
| kö |Det finns 73 Totalt antal pågående åtgärder |
| Tidsgräns |6 för pågående åtgärder finns i unsent kön och ännu inte har skrivit till utgående nätverkstrafik |
| Qs |67 pågående åtgärder har skickats till servern men svaret är inte tillgänglig ännu. Svaret kan bli `Not yet sent by the server` eller `sent by the server but not yet processed by the client.` |
| QC |0 för pågående åtgärder har sett svar men ännu inte har markerats som slutförd på grund av att vänta på slutförande-loop |
| WR |Det finns en aktiv skrivaren (dvs. 6 ej skickade begäranden inte ignoreras) byte/activewriters |
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

    Mer information finns i [Anslut den till cachen med StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Se till att din Azure Redis Cache och klientprogrammet finns i samma region i Azure. Exempel: du kan hämta tidsgränser när cachen är i USA, östra, men klienten finns i USA, västra och begäran inte slutförs inom den `synctimeout` intervall eller du kan hämta timeout-fel när du felsöker från din lokala utvecklingsdator. 
   
    Hög rekommenderas du för att ha cachen och i klienten i samma Azure-region. Om du har ett scenario som inkluderar mellan regioner-anrop, bör du ange den `synctimeout` intervallet till ett högre värde än 1000 ms standardintervallet genom att inkludera en `synctimeout` -egenskapen i anslutningssträngen. I följande exempel visas en StackExchange.Redis cache anslutning sträng fragment med en `synctimeout` på 2 000 ms.
   
        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
2. Se till att du använder den senaste versionen av den [StackExchange.Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar ständigt som i koden för att göra den mer robust för tidsgränser, så det är viktigt att ha den senaste versionen.
3. Om det finns begäranden som är komma bundna av bandbreddsbegränsningar på servern eller klienten, tar det längre tid att slutföra och därmed orsaka timeout. Om din timeout är på grund av nätverkets bandbredd på servern finns i [serverns sida bandbredd överskred](#server-side-bandwidth-exceeded). Om din timeout är på grund av nätverksbandbredd för klienten finns i [klientsidan bandbredd överskred](#client-side-bandwidth-exceeded).
4. Är du komma CPU bundna på servern eller på klienten?
   
   * Kontrollera om du är komma bunden av processor på klienten, vilket kan leda till begäran om att inte bearbetas inom den `synctimeout` intervall, vilket leder till en tidsgräns. Flytta till en större storlek på klientens eller fördela belastningen kan bidra till att styra det här problemet. 
   * Kontrollerar du om du får CPU-bundna på servern genom att övervaka den `CPU` [cachelagra prestandamått](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Begäranden som kommer Redis är CPU-bundna kan orsaka dessa begäranden till timeout. För att åtgärda problemet, kan du fördela belastningen över flera shard i en premium-cache eller uppgradera till en större storlek eller prisnivå. Mer information finns i [servern sida bandbredd överskridit](#server-side-bandwidth-exceeded).
5. Finns det kommandon tar längre tid att bearbeta på servern? Tidskrävande kommandon som tar lång tid att bearbeta på redis-servern kan orsaka timeout. Några exempel på kommandon som körs under lång tid är `mget` med stort antal nycklar, `keys *` eller felaktiga lua-skript. Du kan ansluta till din Azure Redis Cache-instans med hjälp av redis-cli-klienten eller använda den [Redis-konsolen](cache-configure.md#redis-console) och kör den [SlowLog](http://redis.io/commands/slowlog) kommando för att se om det finns begäranden som tar längre tid än förväntat. Redis-servern och StackExchange.Redis är optimerade för många små begäranden i stället för färre stora begäranden. Dela dina data i mindre segment kan förbättra saker här. 
   
    Information om hur du ansluter till Azure Redis Cache-SSL-slutpunkten med hjälp av redis-cli och stunnel finns i den [Vi presenterar ASP.NET-Sessionstillståndsprovider Redis förhandsversionen](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) blogginlägg. Mer information finns i [SlowLog](http://redis.io/commands/slowlog).
6. Hög Redis-serverbelastningen kan orsaka timeout. Du kan övervaka belastningen på servern genom att övervaka den `Redis Server Load` [cachelagra prestandamått](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). En serverbelastning på 100 (högsta värde) innebär det att redis-servern har varit upptagen med någon inaktiv tid, bearbetning av begäranden. Om du vill se om vissa begäranden som tar upp alla server-kapacitet, kör du kommandot SlowLog enligt beskrivningen i föregående stycke. Mer information finns i [hög CPU-användning / serverbelastningen](#high-cpu-usage-server-load).
7. Var det andra händelser på klientsidan som kan ha orsakat en nätverk blip? Kontrollera på klienten (webb, worker-roll eller en Iaas VM) om det uppstod en händelse som skala antalet klientinstanser som upp eller ned eller distribuerar en ny version av klienten eller automatisk skalning är aktiverat? I våra tester, vi har hittat som automatisk skalning eller skala upp/ned kan utgående nätverksanslutning för orsak kan vara förlorad under flera sekunder. StackExchange.Redis-koden är motståndskraftiga mot sådana händelser och återansluter. Under denna tid för återansluter kan alla begäranden i kön timeout.
8. Var det en stor begäran föregående flera små begäranden till Redis Cache som gjort timeout? Parametern `qs` i felet meddelande som anger hur många förfrågningar skickades från klienten till servern, men ännu inte behandlats ett svar. Det här värdet kan hålla växer eftersom StackExchange.Redis använder en enda TCP-anslutning och endast kan läsa ett svar i taget. Även om den första tidsgränsen uppnåddes, slutar den inte de data som skickas till och från servern och andra begäranden blockeras tills stora begäran har slutförts, orsakar timeout. En lösning är att minimera risken för tidsgränser genom att säkerställa att din cache är tillräckligt stort för din arbetsbelastning och dela upp stora värden i mindre segment. En annan möjlig lösning är att använda en pool med `ConnectionMultiplexer` objekt i din klient och Välj minst inlästa `ConnectionMultiplexer` när du skickar en ny begäran. Detta bör förhindra att en enda tidsgräns orsakar övriga förfrågningar till också timeout.
9. Om du använder `RedisSessionStateprovider`, kontrollerar du har angett tidsgräns för återförsök på rätt sätt. `retrytimeoutInMilliseconds` bör vara högre än `operationTimeoutinMilliseonds`, annars Inga återförsök sker. I följande exempel `retrytimeoutInMilliseconds` är inställd på 3000. Mer information finns i [ASP.NET-Sessionstillståndsprovider för Azure Redis Cache](cache-aspnet-session-state-provider.md) och [hur du använder konfigurationsparametrarna för Sessionstillståndsprovider och Utdatacacheprovider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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


1. Kontrollera minnesanvändning på Azure Redis Cache-servern genom att [övervakning](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` och `Used Memory`. Om en Borttagningsprincip är på plats, Redis startar avlägsna nycklar när `Used_Memory` når cachestorleken. Vi rekommenderar `Used Memory RSS` bör endast vara något högre än `Used memory`. En stor skillnad betyder minnesfragmentering (interna eller externa). När `Used Memory RSS` är mindre än `Used Memory`, innebär det en del av cache-minne har bytts av operativsystemet. Om det här växlar inträffar kan du förvänta dig några betydande fördröjning. Eftersom Redis inte har kontroll över hur dess allokeringar mappas till minnessidor som hög `Used Memory RSS` är ofta resultatet av en topp i minnesanvändning. När Redis Frigör minne, minnet ges tillbaka till allokeraren och allokeraren kanske eller kanske inte ger minnet tillbaka till systemet. Det kan finnas en avvikelse mellan det `Used Memory` värde och minnesförbrukningen som rapporteras av operativsystemet. Det kan vara på grund av faktumet minne används och är av Redis, men inte gett tillbaka till systemet. För att minska minnesproblem, kan du utföra följande steg:
   
   * Uppgradera cacheminnet till en större storlek så att du inte kör oss jämfört med begränsningar i minnet i systemet.
   * Ange förfallodatum gånger på nycklarna så att äldre värden avlägsnas proaktivt.
   * Övervaka den `used_memory_rss` cachelagra mått. När det här värdet närmar sig sin cachens storlek kommer förmodligen du börja Se prestandaproblem. Distribuera data över flera shard om du använder en premium-cache, eller uppgradera till en större cachestorlek.
   
   Mer information finns i [minnesbelastning på servern](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Ytterligare information
* [Vilket erbjudande och vilken storlek ska jag använda för Redis-cache?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
* [Hur kan jag göra benchmark-test och testa prestanda hos mitt cacheminne?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Hur kan jag köra Redis-kommandon?](cache-faq.md#how-can-i-run-redis-commands)
* [Så här övervakar du Azure Redis Cache](cache-how-to-monitor.md)

