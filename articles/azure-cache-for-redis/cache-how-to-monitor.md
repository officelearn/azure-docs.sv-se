---
title: Så här övervakar du Azure cache för Redis | Microsoft Docs
description: Lär dig hur du övervakar hälsa och prestanda för Azure-cache för Redis-instanser
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 5ae1827c7af3346a9197a9e28ee442d8aca0e38f
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299521"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Så här övervakar du Azure cache för Redis
Azure cache för Redis använder [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) för att tillhandahålla flera alternativ för att övervaka dina cache-instanser. Du kan visa mått, fästa mått diagram på Start sidan, anpassa datum-och tidsintervallet för övervaknings diagram, lägga till och ta bort mått från diagrammen och ange aviseringar när vissa villkor uppfylls. Med de här verktygen kan du övervaka Azure-cachens hälso tillstånd för Redis-instanser och hjälpa dig att hantera dina cachelagring-program.

Mått för Azure cache för Redis-instanser samlas in med hjälp av kommandot Redis [information](https://redis.io/commands/info) cirka två gånger per minut och lagras automatiskt i 30 dagar (se [Exportera cache-mått](#export-cache-metrics) för att konfigurera en annan bevarande princip) så att de kan visas i mått diagram och utvärderas av varnings regler. Mer information om de olika informations värden som används för varje cache-mått finns i [tillgängliga mått och rapporterings intervall](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Om du vill visa cache-mått [bläddrar](cache-configure.md#configure-azure-cache-for-redis-settings) du till din cache-instans i [Azure Portal](https://portal.azure.com).  Azure cache för Redis innehåller några inbyggda diagram på bladet **Översikt** och **Redis mått** bladet. Varje diagram kan anpassas genom att lägga till eller ta bort mått och ändra rapporterings intervallet.

![Redis mått](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Visa förkonfigurerade mått diagram

**Översikts** bladet har följande förkonfigurerade övervaknings diagram.

* [Övervaknings diagram](#monitoring-charts)
* [Användnings diagram](#usage-charts)

### <a name="monitoring-charts"></a>Övervaknings diagram
Avsnittet **övervakning** i bladet **Översikt** har **träffar och har träffar**, **hämtar och ställer in**, **anslutningar**och **Totalt antal kommando** diagram.

![Övervaknings diagram](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Användnings diagram
**Användnings** avsnittet på bladet **Översikt** har **redis server belastning**, **minnes användning**, **nätverks bandbredd**och **CPU-användning** och visar även **pris nivån** för cache-instansen.

![Användnings diagram](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Pris** nivån visar cache-pris nivån och kan användas för att [skala](cache-how-to-scale.md) cachen till en annan pris nivå.

## <a name="view-metrics-with-azure-monitor"></a>Visa mått med Azure Monitor
Om du vill visa Redis-mått och skapa anpassade diagram med Azure Monitor klickar du på **mått** på **resurs-menyn**och anpassar ditt diagram med önskade mått, rapporterings intervall, diagram typ med mera.

![Redis mått](./media/cache-how-to-monitor/redis-cache-monitor.png)

Mer information om hur du arbetar med mått med Azure Monitor finns i [Översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportera cache-mått
Som standard lagras cache-mått i Azure Monitor i [30 dagar](../azure-monitor/platform/data-platform-metrics.md) och tas sedan bort. Om du vill spara dina cache-mått i mer än 30 dagar kan du ange [ett lagrings konto](../azure-monitor/platform/archive-diagnostic-logs.md) och ange en princip för **bevarande (dagar)** för dina cache-mått. 

Så här konfigurerar du ett lagrings konto för dina cache-mått:

1. Klicka på **diagnostik** på **resurs menyn** på bladet **Azure cache för Redis** .
2. Klicka **på på**.
3. Markera **Arkivera till ett lagringskonto**.
4. Välj det lagrings konto där du vill lagra cache-måtten.
5. Markera kryss rutan **1 minut** och ange en princip för **kvarhållning (dagar)** . Om du inte vill tillämpa någon bevarande princip och bevara data permanent, ställer du in **kvarhållning (dagar)** på **0**.
6. Klicka på **Spara**.

![Redis-diagnostik](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Förutom att arkivera dina cache-mått till lagring kan du också [strömma dem till en händelsehubben eller skicka dem till Azure Monitor loggar](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>
>

Du kan få åtkomst till dina mått genom att visa dem i Azure Portal som tidigare beskrivits i den här artikeln och du kan också komma åt dem med hjälp av [Azure Monitor mått REST API](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Om du ändrar lagrings konton är data i det tidigare konfigurerade lagrings kontot fortfarande tillgängliga för nedladdning, men det visas inte i Azure Portal.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Tillgängliga mått och rapporterings intervall
Cache-mått rapporteras med hjälp av flera rapport intervall, inklusive den **senaste timmen**, **idag**, **senaste veckan**och **anpassad**. **Mått** bladet för varje mått diagram visar medelvärde, lägsta och högsta värden för varje mått i diagrammet, och vissa mått visar total summan för rapporterings intervallet. 

Varje mått innehåller två versioner. Ett mått mäter prestanda för hela cacheminnet och för cacheminnen som använder [kluster](cache-how-to-premium-clustering.md), en andra version av måttet som inkluderar `(Shard 0-9)` i namnet mäter prestanda för en enskild Shard i en cache. Om till exempel en cache har 4 Shards, `Cache Hits` är den totala mängden träffar för hela cacheminnet och `Cache Hits (Shard 3)` är bara träffarna för Shard i cacheminnet.

> [!NOTE]
> Även om cachen är inaktiv utan anslutna aktiva klient program kan du se viss cache-aktivitet, till exempel anslutna klienter, minnes användning och åtgärder som utförs. Den här aktiviteten är normal under driften av en Azure-cache för Redis-instans.
> 
> 

| Mått | Beskrivning |
| --- | --- |
| Cacheträffar |Antalet lyckade nyckels ökningar under det angivna rapporterings intervallet. Detta mappar till `keyspace_hits` från Redis [information](https://redis.io/commands/info) -kommandot. |
| Fördröjning för cache (för hands version) | Svars tiden för cachen beräknas utifrån mellannodens svars tid för cachen. Måttet mäts i mikrosekunder och har tre dimensioner: "AVG", "min" och "Max" som representerar den genomsnittliga, minsta och högsta svars tiden för cachen under det angivna rapporterings intervallet. |
| Cachemissar |Antalet misslyckade nyckels ökningar under det angivna rapporterings intervallet. Detta mappar till `keyspace_misses` från Redis information-kommandot. Missar i cacheminnet innebär inte nödvändigt vis att det finns ett problem med cachen. Till exempel, när du använder programmerings mönstret för cacheminnet, ser ett program först i cachen för ett objekt. Om objektet inte är där (cache saknas) hämtas objektet från databasen och läggs till i cacheminnet för nästa gången. Cachemissar är normalt beteendet för programmerings mönstret cache-undan. Om antalet Cachemissar är högre än förväntat granskar du program logiken som fylls i och läser från cachen. Om objekt avlägsnas från cachen på grund av minnes belastning kan det finnas vissa Cachemissar, men ett bättre mått för att övervaka minnes belastningen är `Used Memory` eller. `Evicted Keys` |
| Läsning cache |Mängden data som läses från cachen i megabyte per sekund (MB/s) under det angivna rapporterings intervallet. Det här värdet härleds från de nätverkskort som har stöd för den virtuella datorn som är värd för cachen och som inte är Redis. **Det här värdet motsvarar den nätverks bandbredd som används av denna cache. Om du vill konfigurera aviseringar för nätverks bandbredds gränser på Server sidan skapar du den med hjälp av `Cache Read` den här räknaren. Se [den här tabellen](cache-faq.md#cache-performance) för de observerade bandbredds gränserna för olika cache-pris nivåer och storlekar.** |
| Skrivcache |Mängden data som skrivs till cachen i megabyte per sekund (MB/s) under det angivna rapporterings intervallet. Det här värdet härleds från de nätverkskort som har stöd för den virtuella datorn som är värd för cachen och som inte är Redis. Det här värdet motsvarar nätverks bandbredden för data som skickas till cacheminnet från klienten. |
| Anslutna klienter |Antalet klient anslutningar till cacheminnet under det angivna rapporterings intervallet. Detta mappar till `connected_clients` från Redis information-kommandot. När [anslutnings gränsen](cache-configure.md#default-redis-server-configuration) nås kommer efterföljande anslutnings försök till cacheminnet att Miss lyckas. Observera att även om det inte finns några aktiva klient program kan det fortfarande finnas några instanser av anslutna klienter på grund av interna processer och anslutningar. |
| Processor |CPU-användningen av Azure-cachen för Redis-servern som en procent andel under det angivna rapporterings intervallet. Det här värdet mappar till operativ systemets `\Processor(_Total)\% Processor Time` prestanda räknare. |
| Fel | Specifika fel och prestanda problem som cachen kan ha under ett angivet rapporterings intervall. Det här måttet har åtta dimensioner som representerar olika fel typer, men kan ha fler tillägg i framtiden. De fel typer som visas nu är följande: <br/><ul><li>**Redundans** – när en cache växlar över (underordnad till Master)</li><li>**DataLoss** – när det finns DataLoss i cacheminnet</li><li>**UnresponsiveClients** – när klienterna inte läser data från servern tillräckligt snabbt</li><li>**AOF** – när det finns ett problem relaterat till AOF persistence</li><li>**RDB** – när det finns ett problem relaterat till RDB persistence</li><li>**Importera** – när det finns ett problem som rör import RDB</li><li>**Exportera** – när det finns ett problem som rör export RDB</li></ul> |
| Avlägsnade nycklar |Antalet objekt som tagits bort från cacheminnet under det angivna rapporterings intervallet på grund `maxmemory` av gränsen. Detta mappar till `evicted_keys` från Redis information-kommandot. |
| Förfallna nycklar |Antalet objekt som har upphört att gälla från cachen under det angivna rapporterings intervallet. Det här värdet mappar `expired_keys` till från Redis information-kommandot.|
| Sparas |Antalet get-åtgärder från cachen under det angivna rapporterings intervallet. Det här värdet är summan av `cmdstat_get`följande värden från Redis `cmdstat_hgetall`-information alla kommando:, `cmdstat_getrange` `cmdstat_hget` `cmdstat_hmget` `cmdstat_mget` `cmdstat_getbit`,,,,, och motsvarar summan av cacheträffar och missar under rapporterings intervallet. |
| Åtgärder per sekund | Det totala antalet kommandon som bearbetas per sekund av cache-servern under det angivna rapporterings intervallet.  Det här värdet mappar till "instantaneous_ops_per_sec" från kommandot Redis information. |
| Redis server-belastning |Procent andelen cykler där Redis-servern är upptagen och väntar inte på inaktivitet för meddelanden. Om den här räknaren når 100 betyder det att Redis-servern har uppnått ett prestanda tak och processorn inte kan bearbeta arbetet snabbare. Om du ser hög redis server-belastning visas timeout-undantag i klienten. I detta fall bör du överväga att skala upp eller partitionera data i flera cacheminnen. |
| Uppsättningar |Antalet set-åtgärder som ska cachelagras under det angivna rapporterings intervallet. Det här `cmdstat_set`värdet är summan av följande värden från Redis information all-kommando:, `cmdstat_setbit` `cmdstat_mset` `cmdstat_hset`, `cmdstat_setex` `cmdstat_hmset` `cmdstat_hsetnx` `cmdstat_lset`,,,, `cmdstat_msetnx`,,, `cmdstat_setrange` , och `cmdstat_setnx`. |
| Totalt antal nycklar  | Det maximala antalet nycklar i cacheminnet under den förflutna tids perioden för rapportering. Detta mappar till `keyspace` från Redis information-kommandot. På grund av en begränsning i det underliggande mått systemet, returnerar totalt antal nycklar det maximala antalet nycklar för Shard som hade det maximala antalet nycklar under rapporterings intervallet.  |
| Totalt antal åtgärder |Det totala antalet kommandon som bearbetas av cache-servern under det angivna rapporterings intervallet. Det här värdet mappar `total_commands_processed` till från Redis information-kommandot. Observera att när Azure cache för Redis används enbart för pub/sub, kommer det inte att finnas några mått `Cache Hits`för `Cache Misses`, `Gets`,, `Sets`eller, men det kommer `Total Operations` att finnas mått som återspeglar cache-användningen för pub/sub-åtgärder. |
| Använt minne |Mängden cache-minne som används för nyckel/värde-par i cacheminnet i MB under det angivna rapporterings intervallet. Det här värdet mappar `used_memory` till från Redis information-kommandot. Detta inkluderar inte metadata eller fragmentering. |
| Använd minnes procent | % Av den totala mängden minne som används under det angivna rapporterings intervallet.  Detta refererar till värdet "used_memory" från kommandot Redis information för att beräkna procent andelen. |
| RSS för använt minne |Mängden cache-minne som används i MB under det angivna rapporterings intervallet, inklusive fragmentering och metadata. Det här värdet mappar `used_memory_rss` till från Redis information-kommandot. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Aviseringar
Du kan konfigurera för att ta emot varningar baserat på mått och aktivitetsloggar. Med Azure Monitor kan du konfigurera att en avisering ska göra följande när den utlöses:

* Skicka ett e-postmeddelande
* Anropa en webbhook
* Anropa en Azure Logic App

Om du vill konfigurera aviserings regler för din cache klickar du på **varnings regler** på **resurs menyn**.

![Övervakning](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Mer information om hur du konfigurerar och använder aviseringar finns i [Översikt över aviseringar](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Aktivitetsloggar
Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure-cachen för Redis-instanser. Det kallades tidigare för "gransknings loggar" eller "drift loggar". Med aktivitets loggar kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, posta och ta bort) som utförs på Azure-cachen för Redis-instanser. 

> [!NOTE]
> Aktivitets loggar innehåller inte Läs åtgärder (GET).
>
>

Om du vill visa aktivitets loggar för cacheminnet klickar du på **aktivitets loggar** på **resurs menyn**.

Mer information om aktivitets loggar finns i [Översikt över Azure aktivitets loggen](../azure-monitor/platform/activity-logs-overview.md).











