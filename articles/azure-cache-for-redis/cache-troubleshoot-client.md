---
title: Felsöka problem på Azure Cache for Redis-klientsidan
description: Lär dig hur du löser vanliga problem på klientsidan med Azure Cache for Redis, till exempel Redis-klientminnestryck, trafiksprängning, hög CPU, begränsad bandbredd, stora begäranden eller stor svarsstorlek.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277952"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Felsöka problem på Azure Cache for Redis-klientsidan

I det här avsnittet beskrivs felsökningsproblem som uppstår på grund av ett villkor för Redis-klienten som programmet använder.

- [Minnestryck på Redis-klienten](#memory-pressure-on-redis-client)
- [Trafiken brast](#traffic-burst)
- [Hög processoranvändning för klient](#high-client-cpu-usage)
- [Bandbreddsbegränsning på klientsidan](#client-side-bandwidth-limitation)
- [Stor begäran eller svarsstorlek](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Minnestryck på Redis-klienten

Minnestryck på klientdatorn leder till alla typer av prestandaproblem som kan fördröja bearbetningen av svar från cacheminnet. När minnestrycket träffar kan systemet söka data till disk. Den här _sidan fel_ orsakar systemet att sakta ner avsevärt.

Så här identifierar du minnestrycket på klienten:

- Övervaka minnesanvändningen på datorn för att se till att den inte överskrider tillgängligt minne.
- Övervaka klientens `Page Faults/Sec` prestandaräknare. Under normal drift har de flesta system vissa sidfel. Toppar i sidfel som motsvarar tidsgränsen för begäran kan indikera minnestryck.

Högt minnestryck på klienten kan mildras på flera sätt:

- Gräva i dina minnesanvändningsmönster för att minska minnesförbrukningen på klienten.
- Uppgradera klientens virtuella dator till en större storlek med mer minne.

## <a name="traffic-burst"></a>Trafiken brast

Skurar av trafik `ThreadPool` i kombination med dåliga inställningar kan resultera i fördröjningar i bearbetningen av data som redan skickats av Redis Server men ännu inte förbrukats på klientsidan.

Övervaka hur `ThreadPool` statistiken ändras med tiden med hjälp av [ett exempel `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Du kan `TimeoutException` använda meddelanden från StackExchange.Redis som nedan för att undersöka:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

I föregående undantag finns det flera frågor som är intressanta:

- Observera att `IOCP` i avsnittet `WORKER` och avsnittet `Busy` har du ett `Min` värde som är större än värdet. Den här `ThreadPool` skillnaden innebär att dina inställningar behöver justeras.
- Du kan `in: 64221`också se . Det här värdet anger att 64 211 byte har tagits emot i klientens kärnuttagslagret men inte har lästs av programmet. Den här skillnaden innebär vanligtvis att ditt program (till exempel StackExchange.Redis) inte läser data från nätverket lika snabbt som servern skickar det till dig.

Du kan [konfigurera dina `ThreadPool` inställningar](cache-faq.md#important-details-about-threadpool-growth) för att se till att trådpoolen skalas upp snabbt under burst-scenarier.

## <a name="high-client-cpu-usage"></a>Hög processoranvändning för klient

Hög klient CPU-användning indikerar att systemet inte kan hålla jämna steg med det arbete som det har ombetts att göra. Även om cachen skickade svaret snabbt, kan klienten misslyckas med att bearbeta svaret i tid.

Övervaka klientens systemomfattande CPU-användning med hjälp av mått som är tillgängliga i Azure-portalen eller via prestandaräknare på datorn. Var noga med att inte övervaka *process* CPU eftersom en enda process kan ha låg CPU-användning men systemomfattande CPU kan vara hög. Titta efter toppar i CPU-användning som motsvarar timeout. Hög CPU kan `in: XXX` också `TimeoutException` orsaka höga värden i felmeddelanden enligt beskrivningen i avsnittet [Trafiksprängning.](#traffic-burst)

> [!NOTE]
> StackExchange.Redis 1.1.603 och `local-cpu` senare `TimeoutException` innehåller måttet i felmeddelanden. Se till att du använder den senaste versionen av [Paketet StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar som ständigt fixas i koden för att göra det mer robust till timeouts så att ha den senaste versionen är viktigt.
>

Så här minskar du en klients höga CPU-användning:

- Undersök vad som orsakar CPU-toppar.
- Uppgradera klienten till en större VM-storlek med mer CPU-kapacitet.

## <a name="client-side-bandwidth-limitation"></a>Bandbreddsbegränsning på klientsidan

Beroende på klientdatorers arkitektur kan de ha begränsningar för hur mycket nätverksbandbredd de har tillgängliga. Om klienten överskrider den tillgängliga bandbredden genom att överbelasta nätverkskapaciteten bearbetas data inte på klientsidan lika snabbt som servern skickar den. Den här situationen kan leda till timeout.

Övervaka hur bandbreddsanvändningen ändras med tiden med hjälp av [ett exempel `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Den här koden kanske inte körs i vissa miljöer med begränsade behörigheter (till exempel Azure-webbplatser).

För att minska, minska förbrukningen av nätverksbandbredd eller öka klientens vm-storlek till en med mer nätverkskapacitet.

## <a name="large-request-or-response-size"></a>Stor begäran eller svarsstorlek

En stor begäran/svar kan orsaka timeout. Anta till exempel att tidsgränsen som konfigurerats på klienten är 1 sekund. Ditt program begär två nycklar (till exempel "A" och "B") samtidigt (med samma fysiska nätverksanslutning). De flesta klienter stöder begäran "pipelining", där båda förfrågningarna "A" och "B" skickas en efter en utan att vänta på sina svar. Servern skickar tillbaka svaren i samma ordning. Om svaret "A" är stort kan det äta upp det mesta av tidsgränsen för senare förfrågningar.

I följande exempel skickas begäran "A" och "B" snabbt till servern. Servern börjar skicka svaren "A" och "B" snabbt. På grund av dataöverföringstider måste svaret "B" vänta bakom svaret "A" time out även om servern svarade snabbt.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Denna begäran /svar är svår att mäta. Du kan instrumentera din klientkod för att spåra stora förfrågningar och svar.

Upplösningar för stora svarsstorlekar varieras men inkluderar:

1. Optimera ditt program för ett stort antal små värden, snarare än några stora värden.
    - Den bästa lösningen är att dela upp dina data i relaterade mindre värden.
    - Se inlägget [Vad är det idealiska värdestorleksintervallet för redis? Är 100 KB för stor?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) för mer information om varför mindre värden rekommenderas.
1. Öka storleken på den virtuella datorn för att få högre bandbreddsfunktioner
    - Mer bandbredd på klienten eller servern VM kan minska dataöverföringstider för större svar.
    - Jämför din nuvarande nätverksanvändning på båda datorerna med gränserna för din nuvarande vm-storlek. Mer bandbredd på endast servern eller bara på klienten kanske inte räcker.
1. Öka antalet anslutningsobjekt som programmet använder.
    - Använd en round-robin-metod för att göra begäranden via olika anslutningsobjekt.

## <a name="additional-information"></a>Ytterligare information

- [Felsöka problem på Azure Cache for Redis-serversidan](cache-troubleshoot-server.md)
- [Hur kan jag jämföra och testa prestanda för min cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
