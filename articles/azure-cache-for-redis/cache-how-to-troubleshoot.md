---
title: Felsökning av Azure Cache för Redis | Microsoft Docs
description: Lär dig att lösa vanliga problem med Azure Cache för Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527174"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Felsökning av Azure Cache för Redis

Den här artikeln hjälper till att felsöka problem som kan uppstå när du ansluter med Azure Cache för Redis-instanser i olika kategorier.

- [Felsökning av klientsidan](#client-side-troubleshooting) hjälper dig att identifiera och lösa problem i program som ansluter till ditt cacheminne.
- [Felsökning av serversidan](#server-side-troubleshooting) hjälper dig att identifiera och lösa problem som uppstår på Azure-Cache för Redis-sida.
- [Felsökning av förlust av data](#data-loss-troubleshooting) hjälper dig att identifiera och lösa problem där nycklar förväntades men hittades inte i cacheminnet.
- [Tidsgränsundantag i StackExchange.Redis](#stackexchangeredis-timeout-exceptions) ger specifik vägledning om hur du felsöker problem med StackExchange.Redis-biblioteket.

> [!NOTE]
> Flera av anvisningarna i den här guiden innehåller anvisningar för att köra Redis-kommandon och övervaka olika prestandamått. Mer information och anvisningar finns i artiklarna i den [ytterligare information](#additional-information) avsnittet.
>
>

## <a name="client-side-troubleshooting"></a>Felsökning av klientsidan

Det här avsnittet beskrivs felsökning av problem som uppstår på grund av ett villkor i klientprogrammet.

- [Minnesbelastning på klienten](#memory-pressure-on-the-client)
- [Trafikökningen](#burst-of-traffic)
- [Klienten hög CPU-användning](#high-client-cpu-usage)
- [Klientsidan bandbredd har överskridits](#client-side-bandwidth-exceeded)
- [Stora begäranden/svar](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Minnesbelastning på klienten

Minnesbelastning på klientdatorn leder till alla typer av problem med prestanda som kan fördröja behandlingen av svar från cachen. När minnesbelastning når kan systemet sidan data till disk. Detta _sidan felaktig_ systemet betydligt långsammare.

Identifiera minnesbelastning på klienten:

- Övervaka användning av minne på datorn för att se till att det inte överstiger tillgängligt minne.
- Övervaka klientens `Page Faults/Sec` prestandaräknaren. De flesta system har vissa sidfel under normal drift. Toppar i sidfel motsvarande med begäran tidsgränser kan indikera minnesbelastning.

Hög minnesbelastning på klienten kan undvikas på flera olika sätt:

- Gå till ditt minne användningsmönster för att minska minnesanvändningen på klienten.
- Uppgradera din klient VM till en större storlek med mer minne.

### <a name="burst-of-traffic"></a>Trafikökningen

Trafiktoppar kombineras med dåligt `ThreadPool` inställningar kan orsaka försening i bearbetning av data som redan har skickat av Redis-servern men har ännu inte används på klientsidan.

Övervaka hur din `ThreadPool` statistik ändras över tid med [ett exempel `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Du kan använda `TimeoutException` meddelanden från StackExchange.Redis som den här ytterligare undersöka:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Det finns flera problem som är intressanta i det föregående undantaget:

- Observera att i den `IOCP` avsnittet och `WORKER` avsnitt som du har en `Busy` värde som är större än den `Min` värde. Detta innebär att din `ThreadPool` inställningar behöver justeras.
- Du kan också se `in: 64221`. Det här värdet anger att 64,211 byte har tagits emot i klientens kernel socket layer men har inte lästs av programmet. Detta innebär normalt att ditt program (till exempel StackExchange.Redis) inte är läsa data från nätverket så snabbt som servern skickar det till dig.

Du kan [konfigurera din `ThreadPool` inställningar](https://gist.github.com/JonCole/e65411214030f0d823cb) mycket kraft för att se till att din trådpoolen skalar upp snabbt under scenarier.

### <a name="high-client-cpu-usage"></a>Klienten hög CPU-användning

Klienten hög CPU-användning indikerar att systemet inte kan hålla med det arbete som har blivit ombedd att göra. Även om svaret skickas snabbt i cacheminnet, kanske klienten inte kan bearbeta svar i tid.

Övervaka klientens systemomfattande CPU-användning med hjälp av mätvärden som är tillgängliga i Azure portal eller via prestandaräknare på datorn. Var noga med att inte övervaka *processen* CPU eftersom en enda process kan ha låg CPU-belastning men systemomfattande Processorn kan vara hög. Titta på för processoranvändningen toppar som kan ha orsakat timeout. Hög CPU kan också göra att hög `in: XXX` värdena i `TimeoutException` felmeddelanden som beskrivs i den [trafikökningen](#burst-of-traffic) avsnittet.

> [!NOTE]
> StackExchange.Redis 1.1.603 och senare inkluderar den `local-cpu` mått i `TimeoutException` felmeddelanden. Se till att du använder den senaste versionen av den [StackExchange.Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar ständigt som i koden för att göra den mer robust för tidsgränser, så det är viktigt att ha den senaste versionen.
>
>

Minimera en klients hög CPU-användning:

- Undersök vad som orsakar processoranvändning.
- Uppgradera klienten till en större VM-storlek med mer CPU-kapacitet.

### <a name="client-side-bandwidth-exceeded"></a>Klientsidan bandbredd har överskridits

Beroende på arkitekturen för klientdatorer, de kan ha begränsningar på hur mycket nätverksbandbredd som de har tillgängliga. Om klienten överskrider den tillgängliga bandbredden genom att överbelasta nätverkskapacitet, är inte sedan data behandlas på klientsidan så snabbt som servern skickar den. Den här situationen kan leda till tidsgränser.

Övervaka hur din bandbreddsanvändning ändras med hjälp av [ett exempel `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Den här koden kan inte köras i vissa miljöer med begränsad behörighet (t.ex. Azure-webbplatser).

För att lösa, minska nätverksbandbredden eller öka klienten VM-storlek till en med mer nätverkskapacitet.

### <a name="large-requestresponse-size"></a>Stora begäranden/svar

En stor begäran/svar kan orsaka timeout. Anta till exempel din timeoutvärdet som konfigurerats på klienten är 1 sekund. Ditt program begär två nycklar (till exempel ”A” och ”B”) på samma gång (med samma fysiska nätverksanslutning). De flesta klienter stöder begäran ”pipelining”, där båda förfrågningarna ”A” och ”B” skickas en efter en utan att vänta tills deras svar. Servern skickar svar tillbaka i samma ordning. Om svaret ”A” är stor kan äta det mesta av timeout för begäranden om senare.

I följande exempel skickas begäran ”A” och ”B” snabbt till servern. Servern börjar skicka svar ”A” och ”B” snabbt. På grund av tider för överföring av data svarstider ”B” måste vänta bakom svar ”A” trots att servern svarat snabbt.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Den här begäran/svar är svåra att mäta. Du kan instrumentera klientkoden för att spåra stora begäranden och svar.

Lösningar för stora svarsstorlekar varieras men omfattar:

1. Optimera ditt program för ett stort antal små värden snarare än några stora värden.
    - Det en bättre lösningen är att dela upp data till relaterade lägre värden.
    - Se inlägget [vad är perfekt värdeintervallet storlek för redis? Är 100 KB för stor? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) för information om varför lägre värden rekommenderas.
1. Öka storleken på den virtuella datorn ska få högre bandbredd-funktioner
    - Mer bandbredd på din klient eller server-dator kan minska tider för överföring av data för större svar.
    - Jämför aktuella nätverksanvändningen på båda datorerna till gränserna för din aktuella VM-storlek. Mer bandbredd på endast-servern eller bara på klienten kanske inte är tillräckligt.
1. Öka antalet anslutningsobjekt som används i ditt program.
    - Använd en resursallokeringsmetod för att göra förfrågningar över olika anslutningsobjekt.

## <a name="server-side-troubleshooting"></a>Felsökning av serversidan

Det här avsnittet beskrivs felsökning av problem som uppstår på grund av ett villkor på servern.

- [Minnesbelastning på servern](#memory-pressure-on-the-server)
- [Hög CPU-användning / serverbelastningen](#high-cpu-usage--server-load)
- [Serverns sida bandbredd har överskridits](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Minnesbelastning på servern

Minnesbelastning på serversidan leder till alla typer av problem med prestanda som kan fördröja behandlingen av förfrågningar. När minnesbelastning når kan systemet sidan data till disk. Detta _sidan felaktig_ systemet betydligt långsammare. Det finns flera möjliga orsaker till det här minnesbelastning:

- Cachen fylls med data nära sin maximala kapacitet.
- Redis ser hög minnesfragmentering. Den här fragmentering orsakas oftast genom att lagra stora objekt eftersom Redis är optimerad för små objekt.

Redis visar två statistik via den [INFO](https://redis.io/commands/info) kommando som kan hjälpa dig att identifiera problemet: ”used_memory” och ”used_memory_rss”. Du kan [visa de här måtten](cache-how-to-monitor.md#view-metrics-with-azure-monitor) med hjälp av portalen.

Det finns flera möjliga ändringar som du kan göra för att hålla minnesanvändning felfri:

- [Konfigurera en princip för minne](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) och ange förfallodatum tider på dina nycklar. Den här principen kanske inte är tillräckligt om du har fragmentering.
- [Anger värdet maxmemory-reserverade](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) som är tillräckligt stor för att kompensera för minnesfragmentering. Mer information finns i de ytterligare [överväganden för minnesreservationerna](#considerations-for-memory-reservations) nedan.
- Dela upp dina stora cachelagrade objekt till mindre relaterade objekt.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) använt minne i mått som ska meddelas tidigt om potentiella konsekvenser.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer minneskapacitet.

#### <a name="considerations-for-memory-reservations"></a>Överväganden för Minnesreservationerna

Uppdatera värden för reservation av minne, som maxmemory-reserverade, kan påverka prestanda för cacheminnet. Anta att du behöver ett cacheminne på 53 GB som fylls med 49 GB data. Ändra värdet för reservation till 8 GB utelämnar systemets max tillgängligt minne på 45 GB. Om _used_memory_ eller _used_memory_rss_ värden är högre än 45 GB, systemet kan ta bort data förrän både _used_memory_ och _used_memory_rss_ är lägre än 45 GB. Borttagning kan öka server belastning och minne fragmentering.

### <a name="high-cpu-usage--server-load"></a>Hög CPU-användning / serverbelastningen

En hög serverbelastningen eller CPU-användning innebär att servern inte kan bearbeta begäranden i tid. Servern kan vara långsamma att svara och det går inte att hålla jämna steg med begäranhastigheter.

[Övervaka mått](cache-how-to-monitor.md#view-metrics-with-azure-monitor) som CPU eller server belastning. Titta på för processoranvändningen toppar som kan ha orsakat timeout.

Det finns flera ändringar som du kan göra för att minska belastningen på hög servern:

- Undersök vad som orsakar processoranvändning, till exempel körs [expansiva kommandon](#expensive-commands) eller felaktigt på grund av hög minnesbelastning.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) på mått som CPU eller server load meddelas tidigt om potentiella konsekvenser.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer CPU-kapacitet.

#### <a name="expensive-commands"></a>Expansiva kommandon

Inte alla Redis-kommandon skapas lika – vissa är dyrare att köra än andra. Den [dokumentation för Redis-kommandon](https://redis.io/commands) visar varje kommando komplexitet. Du bör du granska de kommandon som du kör på din cache och förstå prestandapåverkan från återställning av dessa kommandon. Exempelvis kan den [nycklar](https://redis.io/commands/keys) används ofta utan att känna till att det är en O(N)-åtgärd. Du kan undvika nycklar med hjälp av [SKANNA](https://redis.io/commands/scan) att minska CPU toppar.

Med hjälp av den [SLOWLOG](https://redis.io/commands/slowlog) kommandot, du kan mäta expansiva kommandon som körs mot servern.

### <a name="server-side-bandwidth-exceeded"></a>Serversidan bandbredd har överskridits

Olika cache-storlekar har olika nätverksbandbredd kapaciteter. Om servern överskrider den tillgängliga bandbredden, skickas inte data till klienten så snart. Klienter begäranden gick tidsgräns eftersom servern inte kan skicka data till klienten tillräckligt snabbt.

Mått för ”Cacheläsning” och ”Cache skriva” kan användas för att se hur mycket bandbredd för serversidan som används. Du kan [visa de här måtten](cache-how-to-monitor.md#view-metrics-with-azure-monitor) i portalen.

Om du vill lösa nära situationer där användning av nätverksbandbredd är maximal kapacitet:

- Ändra klienten anrop beteende för att minska belastningen på nätverket.
- [Skapa aviseringar](cache-how-to-monitor.md#alerts) på mått som cacheläsning eller cacheskrivning meddelas tidigt om potentiella konsekvenser.
- [Skala](cache-how-to-scale.md) till en större cachestorlek med mer nätverkets bandbreddskapacitet.

## <a name="data-loss-troubleshooting"></a>Felsökning av förlust av data

Jag hade förväntat för vissa data kan på min Azure-Cache för Redis-instans, men det verkar inte vara det.

Se [vad hände med mina data i Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) för möjliga orsaker och lösningar.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis timeout-undantag

StackExchange.Redis använder en konfigurationsinställning namngivna `synctimeout` för synkrona åtgärder med standardvärdet på 1 000 ms. Om ett synkron anrop inte slutförs under den här tiden, utlöser StackExchange.Redis-klienten ett timeout-fel som liknar följande exempel:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Det här felmeddelandet innehåller mått som kan hjälpa till att peka orsaken och möjlig lösning av problemet. I följande tabell innehåller information om felmått för meddelandet.

| Fel meddelande mått | Information |
| --- | --- |
| inst |Under det senaste tidsintervallet: 0-kommandon som har utfärdats |
| mgr |Hanteraren för socket klarar `socket.select`, vilket innebär att den ber operativsystem och ange en socket som har att göra. Läsaren är inte aktivt lästes från nätverket eftersom det inte tror att det finns något att göra |
| kö |Det finns 73 Totalt antal pågående åtgärder |
| qu |6 för pågående åtgärder finns i unsent kön och ännu inte har skrivits till den utgående nätverkstrafik |
| qs |67 pågående åtgärder har skickats till servern men ett svar är ännu inte tillgänglig. Svaret kan bli `Not yet sent by the server` eller `sent by the server but not yet processed by the client.` |
| qc |0 för pågående åtgärder har sett svar men ännu inte har markerats som slutförd eftersom de väntar på slutförande-loop |
| WR |Det finns en aktiv skrivaren (dvs. inte ignoreras 6 ej skickade begäranden) byte/activewriters |
| i |Det finns ingen aktiv läsare och noll byte är tillgängliga för att läsa på NIC byte/activereaders |

### <a name="steps-to-investigate"></a>Steg för att undersöka

1. Ett bra tips är att kontrollera att du använder följande mönster för att ansluta när du använder StackExchange.Redis-klienten.

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

    Mer information finns i [Anslut den till cachen med StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Se till att din server och klientprogrammet finns i samma region i Azure. Exempel: du kan hämta tidsgränser när cachen är i USA, östra, men klienten finns i USA, västra och begäran inte slutförs inom den `synctimeout` intervall eller du kan hämta timeout-fel när du felsöker från din lokala utvecklingsdator. 

    Hög rekommenderas du för att ha cachen och i klienten i samma Azure-region. Om du har ett scenario som inkluderar mellan regioner-anrop, bör du ange den `synctimeout` intervallet till ett högre värde än 1000 ms standardintervallet genom att inkludera en `synctimeout` -egenskapen i anslutningssträngen. I följande exempel visas ett kodfragment till en anslutningssträng för StackExchange.Redis som tillhandahålls av Azure Cache för Redis med en `synctimeout` på 2 000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Se till att du använder den senaste versionen av den [StackExchange.Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar ständigt som i koden för att göra den mer robust för tidsgränser, så det är viktigt att ha den senaste versionen.
1. Om dina begäranden är bundna av bandbreddsbegränsningar på servern eller klienten, det tar längre tid att slutföra och kan orsaka timeout. Om din timeout är på grund av nätverkets bandbredd på servern finns i [serversidan bandbredd överskred](#server-side-bandwidth-exceeded). Om din timeout är på grund av nätverksbandbredd för klienten finns i [klientsidan bandbredd överskred](#client-side-bandwidth-exceeded).
1. Är du komma CPU bundna på servern eller på klienten?

   - Kontrollera om du är komma bunden av processor på klienten. Hög CPU kan leda till att begäran inte bearbetas inom den `synctimeout` intervall och orsaken en begäran om att tidsgränsen nåddes. Flytta till en större storlek på klientens eller fördela belastningen kan bidra till att styra det här problemet.
   - Kontrollerar du om du får CPU-bundna på servern genom att övervaka den `CPU` [cachelagra prestandamått](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Begäranden som kommer Redis är CPU-bundna kan orsaka dessa begäranden uppnår en tidsgräns. För att åtgärda problemet, kan du fördela belastningen över flera shard i en premium-cache eller uppgradera till en större storlek eller prisnivå. Mer information finns i [servern sida bandbredd överskridit](#server-side-bandwidth-exceeded).
1. Finns det kommandon tar längre tid att bearbeta på servern? Långvariga kommandon som tar lång tid att bearbeta på redis-servern kan orsaka timeout. Mer information om tidskrävande kommandon finns i [expansiva kommandon](#expensive-commands). Du kan ansluta till din Azure-Cache för Redis-instans med hjälp av redis-cli-klienten eller [Redis-konsolen](cache-configure.md#redis-console). Kör sedan den [SLOWLOG](https://redis.io/commands/slowlog) kommando för att se om det finns begäranden långsammare än förväntat. Redis-servern och StackExchange.Redis är optimerade för många små begäranden i stället för färre stora begäranden. Dela dina data i mindre segment kan förbättra saker här.

    Information om hur du ansluter till SSL-slutpunkten för din cache med redis-cli och stunnel finns i bloggposten [Vi presenterar ASP.NET-Sessionstillståndsprovider Redis förhandsversionen](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Hög Redis-serverbelastningen kan orsaka timeout. Du kan övervaka belastningen på servern genom att övervaka den `Redis Server Load` [cachelagra prestandamått](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). En serverbelastning på 100 (högsta värde) innebär det att redis-servern har varit upptagen med någon inaktiv tid, bearbetning av begäranden. Om du vill se om vissa begäranden som tar upp alla server-kapacitet, kör du kommandot SlowLog enligt beskrivningen i föregående stycke. Mer information finns i hög CPU-användning / serverbelastningen.
1. Var det andra händelser på klientsidan som kan ha orsakat en nätverk blip? Vanligaste händelser inkluderar: skala antalet klientinstanser som upp eller ned, distribuera en ny version av klienten eller automatisk skalning aktiverat. Vi har hittat automatisk skalning eller skala upp/ned att utgående nätverksanslutning förlorade under flera sekunder i våra tester. StackExchange.Redis-koden är motståndskraftiga mot sådana händelser och återansluter. När du återansluter, alla begäranden i kön kan tar för lång tid.
1. Var det en stor begäran föregående flera små begäranden till cachen som gjort timeout? Parametern `qs` i felet meddelande som anger hur många förfrågningar skickades från klienten till servern, men har inte bearbetats ett svar. Det här värdet kan hålla växer eftersom StackExchange.Redis använder en enda TCP-anslutning och endast kan läsa ett svar i taget. Även om den första tidsgränsen uppnåddes, slutar den inte mer data skickas till eller från servern. Andra begäranden blockeras tills stora begäran har slutförts och kan orsaka timeout. En lösning är att minimera risken för tidsgränser genom att säkerställa att din cache är tillräckligt stort för din arbetsbelastning och dela upp stora värden i mindre segment. En annan möjlig lösning är att använda en pool med `ConnectionMultiplexer` objekt i din klient och Välj minst inlästa `ConnectionMultiplexer` när du skickar en ny begäran. Läser in över flera anslutningsobjekt bör förhindra att en enda tidsgräns orsakar övriga förfrågningar till också timeout.
1. Om du använder `RedisSessionStateProvider`, kontrollerar du har angett tidsgräns för återförsök på rätt sätt. `retryTimeoutInMilliseconds` bör vara högre än `operationTimeoutInMilliseconds`, annars Inga återförsök sker. I följande exempel `retryTimeoutInMilliseconds` är inställd på 3000. Mer information finns i [ASP.NET-Sessionstillståndsprovider för Azure Cache för Redis](cache-aspnet-session-state-provider.md) och [hur du använder konfigurationsparametrarna för Sessionstillståndsprovider och Utdatacacheprovider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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

1. Kontrollera minnesanvändning på Azure-Cache för Redis-servern genom att [övervakning](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` och `Used Memory`. Om en Borttagningsprincip är på plats, Redis startar avlägsna nycklar när `Used_Memory` når cachestorleken. Vi rekommenderar `Used Memory RSS` bör endast vara något högre än `Used memory`. En stor skillnad betyder minnesfragmentering (interna eller externa). När `Used Memory RSS` är mindre än `Used Memory`, innebär det en del av cache-minne har bytts av operativsystemet. Om det här växlar inträffar kan du förvänta dig några betydande fördröjning. Eftersom Redis inte har kontroll över hur dess allokeringar mappas till minnessidor som hög `Used Memory RSS` är ofta resultatet av en topp i minnesanvändning. När Redis-servern Frigör minne, allokeraren tar minnet men den kan eller kan inte ge minnet tillbaka till systemet. Det kan finnas en avvikelse mellan det `Used Memory` värde och minnesförbrukningen som rapporteras av operativsystemet. Minne kan ha används och getts ut av Redis men inte gett tillbaka till systemet. För att minska minnesproblem, kan du göra följande:

   - Uppgradera cacheminnet till en större storlek så att du inte kör mot begränsningar i minnet i systemet.
   - Ange förfallodatum gånger på nycklarna så att äldre värden avlägsnas proaktivt.
   - Övervaka den `used_memory_rss` cachelagra mått. När det här värdet närmar sig sin cachens storlek, kommer du förmodligen att börja Se prestandaproblem. Distribuera data över flera shard om du använder en premium-cache, eller uppgradera till en större cachestorlek.

   Mer information finns i [minnesbelastning på servern](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Ytterligare information

- [Vilka Azure-Cache för Redis-erbjudande och vilken storlek ska jag använda?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Hur kan jag göra benchmark-test och testa prestanda hos mitt cacheminne?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Hur kan jag köra Redis-kommandon?](cache-faq.md#how-can-i-run-redis-commands)
- [Så här övervakar du Azure Cache för Redis](cache-how-to-monitor.md)
