---
title: Så här övervakar du Azure Cache för Redis | Microsoft Docs
description: Lär dig att övervaka hälsotillstånd och prestanda din Azure-Cache för Redis-instanser
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: cf766997cf416ed201c76c6110641006b7dab41d
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386255"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Så här övervakar du Azure Cache för Redis
Azure Cache för Redis använder [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) tillhandahåller flera alternativ för övervakning av cache-instanser. Du kan visa mått, fästa måttdiagram på startsidan, anpassa intervallet datum och tid för övervakning av diagram, lägga till och ta bort mått från diagrammen och ställa in aviseringar när vissa villkor är uppfyllda. Dessa verktyg kan du övervaka hälsotillståndet för din Azure-Cache för Redis-instanser och hjälper dig att hantera dina cachelagring program.

Mätvärden för Azure Cache för Redis-instanser samlas in med hjälp av Redis [INFO](http://redis.io/commands/info) kommandot ungefär två gånger per minut och lagras automatiskt i 30 dagar (se [exportera cachemått](#export-cache-metrics) att konfigurera en olika bevarandeprincip) så att de kan visas i diagrammen mått och utvärderas från Varningsregler. Läs mer om de olika INFO-värdena som används för varje cache-mått, [tillgängliga mått och rapportering intervall](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Visa cache-mått [Bläddra](cache-configure.md#configure-azure-cache-for-redis-settings) till cache-instans i den [Azure-portalen](https://portal.azure.com).  Azure Cache för Redis ger vissa inbyggda diagram på den **översikt** bladet och **Redis-mått** bladet. Varje diagram kan anpassas genom att lägga till eller ta bort mått och ändra reporting intervallet.

![Redis-mått](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Visa förkonfigurerade måttdiagram

Den **översikt** bladet har följande förkonfigurerade övervakning av diagram.

* [Övervakning av diagram](#monitoring-charts)
* [Användning-diagram](#usage-charts)

### <a name="monitoring-charts"></a>Övervakning av diagram
Den **övervakning** i avsnittet den **översikt** bladet **träffar och missar**, **hämtar och anger**, **anslutningar**, och **Totalt antal kommandon** diagram.

![Övervakning av diagram](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Användning-diagram
Den **användning** i avsnittet den **översikt** bladet **Redis-serverbelastningen**, **minnesanvändning**, **nätverksbandbredd**, och **CPU-användning** diagram och visar även de **prisnivå** för cache-instans.

![Användning-diagram](./media/cache-how-to-monitor/redis-cache-usage-part.png)

Den **prisnivå** visar cache priserna nivå och kan användas för att [skala](cache-how-to-scale.md) cache till en annan prisnivå.

## <a name="view-metrics-with-azure-monitor"></a>Visa mått med Azure monitor
Om du vill visa Redis-mått och skapa anpassade diagram med hjälp av Azure Monitor, klickar du på **mått** från den **resursmenyn**, och anpassa ditt diagram med hjälp av den önskade mätvärden reporting intervall och diagramtyp.

![Redis-mått](./media/cache-how-to-monitor/redis-cache-monitor.png)

Läs mer om hur du arbetar med mått med Azure Monitor, [översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportera cache-mått
Cache-mått i Azure Monitor är som standard [lagras i 30 dagar](../azure-monitor/platform/data-collection.md#metrics) och tas sedan bort. För att bevara din cache-mått längre än 30 dagar, kan du [utse ett storage-konto](../azure-monitor/platform/archive-diagnostic-logs.md) och ange en **Kvarhållning (dagar)** princip för cache-mått. 

Konfigurera ett lagringskonto för cache-mått:

1. Klicka på **diagnostik** från den **resursmenyn** i den **Azure Cache för Redis** bladet.
2. Klicka på **på**.
3. Kontrollera **arkivet till ett lagringskonto**.
4. Välj lagringskonto som ska lagra cache-mått.
5. Kontrollera den **1 minut** kryssrutan och ange en **Kvarhållning (dagar)** princip. Om du inte vill tillämpa någon bevarandeprincip och behålla data permanent genom att ange **Kvarhållning (dagar)** till **0**.
6. Klicka på **Spara**.

![Redis-diagnostik](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Förutom arkivering cache-mått till lagring, kan du också [strömma dem till en Event hub eller skicka dem till Log Analytics](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md#retrieve-metric-values).
>
>

Du kan visa dem i Azure-portalen enligt beskrivningen i den här artikeln för att komma åt dina mått, och du kan också komma åt dem med hjälp av den [Azure Monitor Metrics REST API](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Om du ändrar storage-konton kan data i det tidigare konfigurerade lagringskontot förblir tillgängliga för nedladdning, men visas inte i Azure-portalen.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Tillgängliga mått och rapportering intervall
Cache-mått rapporteras med hjälp av flera reporting intervall, inklusive **senaste timmen**, **idag**, **senaste veckan**, och **anpassad**. Den **mått** bladet för varje diagram med prestandamått visar genomsnittlig, minsta och högsta värden för varje mått i diagrammet och vissa mått visar totalt för reporting intervallet. 

Varje mått innehåller två versioner. Ett mått som mäter prestanda för hela cachen och cacheminnen som använder [klustring](cache-how-to-premium-clustering.md), en andra versionen av det mått som innehåller `(Shard 0-9)` i namnet mått prestanda för en enda shard i ett cacheminne. Till exempel om en cache har 4 shards, `Cache Hits` är den totala mängden träffar för hela cachen och `Cache Hits (Shard 3)` är bara träffar för fragmentet för cachen.

> [!NOTE]
> Du kan även när cachen är inaktiv utan anslutna aktiv klient-program, för att se vissa Cacheaktivitet, till exempel anslutna klienter, minnesanvändning och åtgärder som utförs. Den här aktiviteten är normalt under driften av Azure Cache för Redis-instans.
> 
> 

| Mått | Beskrivning |
| --- | --- |
| Cacheträffar |Antal lyckade sökningar som nyckel för under det angivna intervallet för rapportering. Detta mappas till `keyspace_hits` från Redis [INFO](http://redis.io/commands/info) kommando. |
| Svarstid för cache (förhandsversion) | Svarstiden för cachen beräknas utifrån av Distributionsrestriktioner svarstiden för cachen. Det här måttet mäts i mikrosekunder och har tre dimensioner: ”Genomsnittlig”, ”Min” och ”Max” som representerar genomsnittlig, minsta och högsta svarstiden för cachen respektive under det angivna intervallet för rapportering. |
| Cachemissar |Antal misslyckade viktiga sökningar under det angivna intervallet för rapportering. Detta mappas till `keyspace_misses` från Redis INFO-kommandot. Cachemissar innebär inte nödvändigtvis att det finns ett problem med cacheminnet. Till exempel när du använder mönstret cache-aside programmeringsspråk, ett program att leta i cacheminnet för ett objekt. Om objektet inte är det (cachemiss), är objektet hämtas från databasen och läggs till i cachen till nästa gång. Cachemissar är normalt att programming cache aside-mönstret. Om antalet cachemissar är högre än förväntat, granskar du programlogik som fyller på och läser från cachen. Om objekt avlägsnas från cache på grund av minnesbelastning och det kan finnas vissa cachemissar, men ett bättre mått att övervaka minnesbelastning skulle vara `Used Memory` eller `Evicted Keys`. |
| Cacheläsning |Mängden data som lästs från cacheminnet i megabyte per sekund (MBIT/s) under det angivna intervallet för rapportering. Det här värdet härleds från nätverkskort som har stöd för den virtuella datorn som är värd för cachen och kan inte Redis specifika. **Det här värdet motsvarar nätverksbandbredden som används av det här cacheminnet. Om du vill ställa in aviseringar för serversidan nätverk bandbreddsgränser, skapar den sedan med det här `Cache Read` räknaren. Se [den här tabellen](cache-faq.md#cache-performance) för observerade bandbreddsgränserna för olika cache priser nivåer och storlekar.** |
| Cacheskrivning |Mängden data som skrivs till cachen i megabyte per sekund (MBIT/s) under den angivna reporting intervall. Det här värdet härleds från nätverkskort som har stöd för den virtuella datorn som är värd för cachen och kan inte Redis specifika. Det här värdet motsvarar bandbredd på data som skickas till cachen från klienten. |
| Anslutna klienter |Antal klientanslutningar till cachen under det angivna intervallet för rapportering. Detta mappas till `connected_clients` från Redis INFO-kommandot. När den [anslutningsgräns](cache-configure.md#default-redis-server-configuration) har nått efterföljande anslutningsförsök till cache kommer att misslyckas. Observera att även om det finns inga aktiva klientprogram, det kan fortfarande finnas några instanser av anslutna klienter på grund av interna processer och anslutningar. |
| Processor |Processoranvändningen Azure cache för Redis-server som en procentandel under det angivna intervallet för rapportering. Det här värdet som mappar till operativsystemet `\Processor(_Total)\% Processor Time` prestandaräknaren. |
| Fel | Specifika fel och prestandaproblem som cacheminnet stöta på under en angiven tidsperiod för rapportering. Det här måttet har åtta dimensioner som representerar olika feltyper, men kan ha fler har lagts till i framtiden. Feltyper representeras nu är följande: <br/><ul><li>**Redundans** – när en cache redundansväxlar (underordnad främjar till master-databasen)</li><li>**Krascha** – när cachen kraschar oväntat på någon av noderna</li><li>**Dataloss** – om det finns dataloss i cachen</li><li>**UnresponsiveClients** – när klienterna inte läsa data från servern tillräckligt snabbt</li><li>**AOF** – när ett problem som rör AOF persistence</li><li>**RDB** – när ett problem som rör RDB persistence</li><li>**Importera** – när ett problem som rör Import RDB</li><li>**Exportera** – när ett problem som rör exportera RDB</li></ul> |
| Avlägsnade nycklar |Antal objekt som avlägsnas från cachen under det angivna reporting intervallet på grund av den `maxmemory` gränsen. Detta mappas till `evicted_keys` från Redis INFO-kommandot. |
| Utgångna nycklar |Antalet objekt som upphört att gälla från cachen under det angivna intervallet för rapportering. Det här värdet som mappar till `expired_keys` från Redis INFO-kommandot.|
| Hämtar |Antal get-åtgärder från cachen under det angivna intervallet för rapportering. Det här värdet är summan av följande värden från Redis-INFO alla kommandot: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, och `cmdstat_getrange`, och motsvarar summan av träffar och missar under Rapportintervall. |
| Åtgärder per sekund | Totalt antal kommandon som bearbetas per sekund av cache-server under det angivna intervallet för rapportering.  Det här värdet mappas till ”instantaneous_ops_per_sec” från Redis INFO-kommandot. |
| Redis-serverbelastning |Procentandelen av cykler där Redis-servern är upptagen bearbetning och inte väntar på inaktiv för meddelanden. Om den här räknaren når 100 innebär det att Redis-servern har nått ett tak för prestanda och Processorn kan inte bearbeta fungerar du någon snabbare. Om du ser hög belastning för Redis-Server kan du se tidsgränsundantag i klienten. I det här fallet bör du skala upp eller partitionera dina data i flera cacheminnen. |
| Uppsättningar |Antalet set-åtgärder till cachen under det angivna intervallet för rapportering. Det här värdet är summan av följande värden från Redis-INFO alla kommandot: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` , och `cmdstat_setnx`. |
| Totalt antal nycklar  | Det maximala antalet nycklar i cacheminnet under den senaste rapporteringsperioden. Detta mappas till `keyspace` från Redis INFO-kommandot. På grund av en begränsning av det underliggande mått-systemet för cache med klustring aktiverat, returnerar Totalt antal nycklar det maximala antalet nycklarna för den shard som hade det maximala antalet nycklar under reporting intervallet.  |
| Totalt antal åtgärder |Totalt antal kommandon som bearbetas av cache-server under det angivna intervallet för rapportering. Det här värdet som mappar till `total_commands_processed` från Redis INFO-kommandot. Observera att när Azure Cache för Redis används enbart för pub/sub det ska finnas några mått för `Cache Hits`, `Cache Misses`, `Gets`, eller `Sets`, men det finns `Total Operations` mått som visar cacheanvändning för pub/sub-åtgärder. |
| Använt minne |Mängden cache-minne som används för nyckel/värde-par i cachen i MB under det angivna intervallet för rapportering. Det här värdet som mappar till `used_memory` från Redis INFO-kommandot. Detta inkluderar inte metadata eller fragmentering. |
| Procentandelen använt minne | % Av det totala minnet som används under det angivna intervallet för rapportering.  Detta refererar till värdet ”used_memory” från Redis INFO-kommandot för att beräkna procentuella. |
| Använt minne RSS |Mängden cache-minne som används i MB under det angivna reporting intervallet, inklusive fragmentering och metadata. Det här värdet som mappar till `used_memory_rss` från Redis INFO-kommandot. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Aviseringar
Du kan konfigurera för att ta emot varningar baserat på mått och aktivitet. Med Azure Monitor kan du konfigurera att en avisering ska göra följande när den utlöses:

* Skicka ett e-postmeddelande
* Anropa en webbhook
* Anropa en Azure Logic App

Om du vill konfigurera Varningsregler för cacheminnet, klickar du på **Aviseringsregler** från den **resursmenyn**.

![Övervakning](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Mer information om hur du konfigurerar och använder aviseringar finns i [översikt över aviseringar](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Aktivitetsloggar
Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina Azure-Cache för Redis-instanser. Det som tidigare kallades ”granskningsloggar” eller ”driftloggar”. Med aktivitetsloggar som du kan fastställa den ”vad, vem, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas på din Azure-Cache för Redis-instanser. 

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET).
>
>

Visa aktivitetsloggar för din cachelagring genom att klicka på **aktivitetsloggar** från den **resursmenyn**.

Läs mer om aktivitetsloggar [översikt över Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











