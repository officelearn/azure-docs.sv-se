---
title: Övervaka Azure Cache för Redis
description: Lär dig hur du övervakar hälso- och prestanda i Azure Cache för Redis-instanser
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 86cbeddba699e89ce1127dbac72dac81dcc41449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547497"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Övervaka Azure Cache för Redis

Azure Cache för Redis använder [Azure Monitor](../azure-monitor/index.yml) för att tillhandahålla flera alternativ för övervakning av cacheinstanser. Du kan visa mått, fästa måttdiagram på Startboard, anpassa datum- och tidsintervallet för övervakningsdiagram, lägga till och ta bort mått från diagrammen och ange aviseringar när vissa villkor uppfylls. Med de här verktygen kan du övervaka hälsotillståndet för dina Azure-cache för Redis-instanser och hjälpa dig att hantera dina cachelagringsprogram.

Mått för Azure Cache för Redis-instanser samlas in med kommandot Redis [INFO](https://redis.io/commands/info) ungefär två gånger per minut och lagras automatiskt i 30 dagar (se [Exportera cachemått](#export-cache-metrics) för att konfigurera en annan bevarandeprincip) så att de kan visas i måttdiagrammen och utvärderas av varningsregler. Mer information om de olika INFO-värden som används för varje cachemått finns i [Tillgängliga mått och rapporteringsintervall](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Om du vill visa cachemått [bläddrar du](cache-configure.md#configure-azure-cache-for-redis-settings) till cacheinstansen i [Azure-portalen](https://portal.azure.com).  Azure Cache för Redis innehåller några inbyggda diagram på **bladet Översikt** och **Redis-måttbladet.** Varje diagram kan anpassas genom att lägga till eller ta bort mått och ändra rapporteringsintervallet.

![Redis-mått](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Visa förkonfigurerade måttdiagram

**Bladet Översikt** har följande förkonfigurerade övervakningsdiagram.

* [Övervakningsdiagram](#monitoring-charts)
* [Användningsdiagram](#usage-charts)

### <a name="monitoring-charts"></a>Övervakningsdiagram

Avsnittet **Övervakning** i **bladet Översikt** har diagram över träffar och **missar,** hämtar och **anger,** **anslutningar**och totalt **antal kommandon.**

![Övervakningsdiagram](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Användningsdiagram

Avsnittet **Användning** i **bladet Översikt** har **Redis Server Load**, Memory **Usage**, **Network Bandwidth**och CPU **Usage** diagram, och visar även **prisnivån** för cacheinstansen.

![Användningsdiagram](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Prisnivån** visar cacheprisnivån och kan användas för att [skala](cache-how-to-scale.md) cachen till en annan prisnivå.

## <a name="view-metrics-with-azure-monitor"></a>Visa mått med Azure-övervakare

Om du vill visa Redis-mått och skapa anpassade diagram med Azure Monitor klickar du på Mått från **resursmenyn**och **anpassar** diagrammet med önskat mått, rapporteringsintervall, diagramtyp med mera.

![Redis-mått](./media/cache-how-to-monitor/redis-cache-monitor.png)

Mer information om hur du arbetar med mått med Azure Monitor finns [i Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportera cachemått

Som standard lagras cachemått i Azure Monitor i [30 dagar](../azure-monitor/platform/data-platform-metrics.md) och tas sedan bort. Om du vill spara cachemåtten längre än 30 dagar kan du [ange ett lagringskonto](../azure-monitor/platform/archive-diagnostic-logs.md) och ange en princip för **kvarhållning (dagar)** för cachemåtten. 

Så här konfigurerar du ett lagringskonto för cachemåtten:

1. Välj **Diagnostik**under rubriken **Övervakning** på sidan Azure Cache **för Redis** .
2. Välj **+ Lägg till diagnostikinställning**.
3. Namnge inställningarna.
4. Markera **Arkivera till ett lagringskonto**. Du debiteras normala datahastigheter för lagring och transaktioner när du skickar diagnostik till ett lagringskonto.
4. Välj **Konfigurera** om du vill välja vilket lagringskonto som cachemåtten ska lagras i.
5. Under **tabellrubrikmåttet**visas kryssrutan bredvid de radartiklar som du vill lagra, till exempel **AllMetrics**. Ange en **princip för kvarhållning (dagar).** Den maximala kvarhållning av dagar som du kan ange är **365 dagar**. Men om du vill behålla måttdata för alltid anger du **Kvarhållning (dagar)** till **0**.
6. Klicka på **Spara**.


![Redis diagnostik](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Förutom att arkivera cachemåtten till lagring kan du också [strömma dem till en händelsenav eller skicka dem till Azure Monitor-loggar](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>

För att komma åt dina mått kan du visa dem i Azure-portalen som tidigare beskrivits i den här artikeln, och du kan också komma åt dem med hjälp av [AZURE Monitor Metrics REST API](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Om du ändrar lagringskonton förblir data i det tidigare konfigurerade lagringskontot tillgängliga för hämtning, men de visas inte i Azure-portalen.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Tillgängliga mått och rapporteringsintervall

Cachemått rapporteras med flera rapporteringsintervall, inklusive **föregående timme**, **Idag**, **Föregående vecka**och **Anpassad**. **Bladet Mått** för varje måttdiagram visar medelvärdena, minimi- och maximivärdena för varje mått i diagrammet, och vissa mått visar en summa för rapporteringsintervallet. 

Varje mått innehåller två versioner. Ett mått mäter prestanda för hela cacheminnet och för cacheminnen som `(Shard 0-9)` använder [klustring](cache-how-to-premium-clustering.md), en andra version av måttet som ingår i namnet mäter prestanda för en enskild fragment i en cache. Till exempel om en cache har `Cache Hits` fyra shards, är det `Cache Hits (Shard 3)` totala antalet träffar för hela cachen, och är bara träffar för att fragmentet av cachen.

> [!NOTE]
> Även när cacheminnet är inaktivt utan anslutna aktiva klientprogram kan du se viss cacheaktivitet, till exempel anslutna klienter, minnesanvändning och åtgärder som utförs. Den här aktiviteten är normal under driften av en Azure-cache för Redis-instans.
> 
> 

| Mått | Beskrivning |
| --- | --- |
| Cache träffar |Antalet lyckade nyckelsökningar under det angivna rapporteringsintervallet. Det här `keyspace_hits` numret mappas från kommandot Redis [INFO.](https://redis.io/commands/info) |
| Cache latency (förhandsgranskning) | Svarstiden för cachen beräknas baserat på internode svarstiden för cacheminnet. Det här måttet mäts i mikrosekoner `Avg` `Min`och `Max`har tre dimensioner: , och , som representerar medelvärdet, minimi- och maximifördröjningen för cacheminnet under det angivna rapporteringsintervallet. |
| Cache missar |Antalet misslyckade nyckelsökningar under det angivna rapporteringsintervallet. Det här `keyspace_misses` numret mappas från kommandot Redis INFO. Cache missar betyder inte nödvändigtvis att det finns ett problem med cachen. När du till exempel använder programmeringsmönstret för cache-aside söker ett program först i cacheminnet efter ett objekt. Om objektet inte finns där (cachefel) hämtas objektet från databasen och läggs till i cacheminnet till nästa gång. Cache missar är normalt beteende för cache-aside programmering mönster. Om antalet cachemissar är högre än förväntat undersöker du programlogiken som fyller i och läser från cacheminnet. Om objekt håller på att vräkas från cachen på grund av minnestryck, kan det finnas `Used Memory` vissa `Evicted Keys`cache missar, men ett bättre mått att övervaka för minnestryck skulle vara eller . |
| Cache läsa |Mängden data som läses från cacheminnet i Megabytes per sekund (MB/s) under det angivna rapporteringsintervallet. Det här värdet härleds från nätverkskorten som stöder den virtuella datorn som är värd för cacheminnet och är inte Redis-specifik. **Det här värdet motsvarar den nätverksbandbredd som används av den här cachen. Om du vill ställa in aviseringar för bandbreddsbegränsningar på `Cache Read` serversidan skapar du den med den här räknaren. Se [den här tabellen](cache-faq.md#cache-performance) för de observerade bandbreddsgränserna för olika cacheprisnivåer och storlekar.** |
| Cache skriva |Mängden data som skrivs till cacheminnet i Megabytes per sekund (MB/s) under det angivna rapporteringsintervallet. Det här värdet härleds från nätverkskorten som stöder den virtuella datorn som är värd för cacheminnet och är inte Redis-specifik. Det här värdet motsvarar nätverksbandbredden för data som skickas till cachen från klienten. |
| Anslutna klienter |Antalet klientanslutningar till cacheminnet under det angivna rapporteringsintervallet. Det här `connected_clients` numret mappas från kommandot Redis INFO. När [anslutningsgränsen](cache-configure.md#default-redis-server-configuration) har uppnåtts misslyckas efterföljande anslutningsförsök till cachen. Även om det inte finns några aktiva klientprogram kan det fortfarande finnas några instanser av anslutna klienter på grund av interna processer och anslutningar. |
| Processor |CPU-användningen av Azure Cache for Redis-servern i procent under det angivna rapporteringsintervallet. Det här värdet mappas till prestandaräknaren för operativsystemet. `\Processor(_Total)\% Processor Time` |
| Fel | Specifika fel och prestandaproblem som cachen kan uppleva under ett angivet rapporteringsintervall. Det här måttet har åtta dimensioner som representerar olika feltyper, men kan ha lagts till mer i framtiden. De feltyper som representeras nu är följande: <br/><ul><li>**Redundans** – när en cache växlar över (underordnad befordras till master)</li><li>**Dataloss** – när det finns dataförlust på cacheminnet</li><li>**Svarar inteClients** – när klienterna inte läser data från servern tillräckligt snabbt</li><li>**AOF** - när det finns ett problem som rör AOF uthållighet</li><li>**RDB** - när det finns ett problem som rör RDB uthållighet</li><li>**Importera** – när det finns ett problem som rör import RDB</li><li>**Exportera** – när det finns ett problem som rör export RDB</li></ul> |
| Vräkta nycklar |Antalet objekt som har vräkas från cacheminnet under `maxmemory` det angivna rapporteringsintervallet på grund av gränsen. Det här `evicted_keys` numret mappas från kommandot Redis INFO. |
| Utgångna nycklar |Antalet objekt har upphört att gälla från cacheminnet under det angivna rapporteringsintervallet. Det här `expired_keys` värdet mappas från kommandot Redis INFO.|
| Får |Antalet hämta åtgärder från cacheminnet under det angivna rapporteringsintervallet. Det här värdet är summan av följande värden från `cmdstat_get` `cmdstat_hget`kommandot `cmdstat_hgetall` `cmdstat_hmget`Redis INFO all: , , , `cmdstat_mget` `cmdstat_getbit`, och `cmdstat_getrange`, och motsvarar summan av cacheträffar och missar under rapporteringsintervallet. |
| Operationer per sekund | Det totala antalet kommandon som bearbetas per sekund av cacheservern under det angivna rapporteringsintervallet.  Det här värdet mappas till "instantaneous_ops_per_sec" från kommandot Redis INFO. |
| Inläsning av Redis-server |Procentandelen cykler där Redis-servern är upptagen med att bearbeta och inte väntar inaktiv för meddelanden. Om den här räknaren når 100 betyder det att Redis-servern har träffat ett prestandatak och processorn kan inte bearbeta arbetet snabbare. Om du ser hög Redis Server Load, då kommer du att se timeout undantag i klienten. I det här fallet bör du överväga att skala upp eller partitionera dina data till flera cacheminnen. |
| Uppsättningar |Antalet inställda åtgärder till cacheminnet under det angivna rapporteringsintervallet. Det här värdet är summan av följande värden från `cmdstat_set` `cmdstat_hset`kommandot `cmdstat_hmset` `cmdstat_hsetnx`Redis `cmdstat_msetnx`INFO `cmdstat_setbit` `cmdstat_setex`all: , , , , `cmdstat_setrange` `cmdstat_setnx` `cmdstat_lset` `cmdstat_mset`, , , , , , och . |
| Totalt antal nycklar  | Det maximala antalet nycklar i cacheminnet under den senaste rapporteringsperioden. Det här `keyspace` numret mappas från kommandot Redis INFO. På grund av en begränsning av det underliggande måttsystemet returnerar Total Keys det maximala antalet nycklar för shard som hade det maximala antalet nycklar under rapporteringsintervallet för cacheminnen aktiverat.  |
| Totala operationer |Det totala antalet kommandon som bearbetas av cacheservern under det angivna rapporteringsintervallet. Det här `total_commands_processed` värdet mappas från kommandot Redis INFO. När Azure Cache för Redis används enbart för pub/sub `Cache Hits` `Cache Misses`kommer `Gets`det `Sets`inte att `Total Operations` finnas några mått för , , eller , men det kommer att finnas mått som återspeglar cacheanvändningen för pub/delåtgärder. |
| Använt minne |Mängden cacheminne som används för nyckel-/värdepar i cacheminnet i MB under det angivna rapporteringsintervallet. Det här `used_memory` värdet mappas från kommandot Redis INFO. Det här värdet innehåller inte metadata eller fragmentering. |
| Använd minnesprocent | % av det totala minne som används under det angivna rapporteringsintervallet.  Det här värdet `used_memory` refererar till värdet från kommandot Redis INFO för att beräkna procentsatsen. |
| Använt minne RSS |Mängden cacheminne som används i MB under det angivna rapporteringsintervallet, inklusive fragmentering och metadata. Det här `used_memory_rss` värdet mappas från kommandot Redis INFO. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Aviseringar

Du kan konfigurera för att ta emot varningar baserat på mått och aktivitetsloggar. Med Azure Monitor kan du konfigurera att en avisering ska göra följande när den utlöses:

* Skicka ett e-postmeddelande
* Anropa en webbhook
* Anropa en Azure Logic App

Om du vill konfigurera varningsregler för cacheminnet klickar du på **Varningsregler** på **resursmenyn**.

![Övervakning](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Mer information om hur du konfigurerar och använder aviseringar finns i [Översikt över aviseringar](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Aktivitetsloggar
Aktivitetsloggar ger insikt i de åtgärder som utfördes på dina Azure-cache för Redis-instanser. Det var tidigare känt som "granskningsloggar" eller "operativa loggar". Med hjälp av aktivitetsloggar kan du bestämma vilka, vem och när för alla skrivåtgärder (PUT, POST, DELETE) som tas på dina Azure-cache för Redis-instanser. 

> [!NOTE]
> Aktivitetsloggar innehåller inte läsåtgärder (GET).
>

Om du vill visa aktivitetsloggar för cacheminnet klickar du på **Aktivitetsloggar** på **Resurs-menyn**.

Mer information om aktivitetsloggar finns [i Översikt över Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md).
