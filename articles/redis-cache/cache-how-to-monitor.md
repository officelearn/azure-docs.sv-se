---
title: Hur du övervakar Azure Redis-Cache | Microsoft Docs
description: Lär dig att övervaka hälsotillstånd och prestanda Azure Redis-Cache-instanser
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 3a68435866e6fb5bf0210144e53918c35b416449
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910657"
---
# <a name="how-to-monitor-azure-redis-cache"></a>Så här övervakar du Azure Redis-cache
Azure Redis-Cache använder [Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) att tillhandahålla flera alternativ för övervakning av cacheinstanser. Du kan visa mått, fästa diagram mått på startsidan, anpassa intervallet för datum och tid för övervakning av diagram, lägga till och ta bort mått från diagrammen och Ställ in aviseringar när vissa villkor är uppfyllda. Dessa verktyg kan du övervaka hälsotillståndet för dina Azure Redis-Cache-instanser och hjälper dig att hantera dina cachelagring program.

Mätvärden för Azure Redis-Cache instanser samlas in med hjälp av Redis [INFO](http://redis.io/commands/info) kommandot ungefär två gånger per minut och automatiskt lagras i 30 dagar (finns [exportera cache mått](#export-cache-metrics) att konfigurera en annan bevarandeprincip) så att de kan visas i diagram för mått och utvärdera Varningsregler. Mer information om de olika INFO värdena som används för varje cache-mått finns [tillgängliga mått och rapportering intervall](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Så här visar du statistik, och cachen [Bläddra](cache-configure.md#configure-redis-cache-settings) till cache-instans i den [Azure-portalen](https://portal.azure.com).  Azure Redis-Cache innehåller vissa inbyggda diagram på den **översikt** bladet och **Redis mått** bladet. Varje diagram kan anpassas genom att lägga till eller ta bort mått och ändra reporting intervallet.

![Redis-mått](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Visa förkonfigurerade mått diagram

Den **översikt** bladet har följande förkonfigurerade övervakning diagram.

* [Övervakning av diagram](#monitoring-charts)
* [Diagram för användning](#usage-charts)

### <a name="monitoring-charts"></a>Övervakning av diagram
Den **övervakning** under den **översikt** bladet har **träffar och missar**, **hämtar och anger**, **anslutningar**, och **totala kommandon** diagram.

![Övervakning av diagram](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Diagram för användning
Den **användning** under den **översikt** bladet har **Redis-serverbelastning**, **minnesanvändning**, **nätverksbandbredd**, och **CPU-användning** diagram och visar även den **prisnivå** för cache-instansen.

![Diagram för användning](./media/cache-how-to-monitor/redis-cache-usage-part.png)

Den **prisnivå** visar cache prissättning nivå och kan användas för att [skala](cache-how-to-scale.md) cache till en annan prisnivå.

## <a name="view-metrics-with-azure-monitor"></a>Visa mått med Azure Övervakare
Om du vill visa Redis mått och skapa anpassade diagram med hjälp av Azure-Monitor, klickar du på **mått** från den **resurs menyn**, och anpassa ditt diagram med de önskade mätvärden reporting intervall och diagram.

![Redis-mått](./media/cache-how-to-monitor/redis-cache-monitor.png)

Mer information om hur du arbetar med hjälp av Azure-Monitor finns [översikt över mått i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportera cache-mått
Som standard cache i Azure-Monitor är [lagras i 30 dagar](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) och sedan tas bort. För att bevara din cache-mätvärden för längre än 30 dagar kan du [ange ett lagringskonto](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) och ange en **bevarande (dagar)** princip för cache-mått. 

Så här konfigurerar du ett lagringskonto för cache-mått:

1. Klicka på **diagnostik** från den **resurs menyn** i den **Redis-Cache** bladet.
2. Klicka på **på**.
3. Kontrollera **arkivet till ett lagringskonto**.
4. Välj lagringskonto för lagring av cache-mått.
5. Kontrollera den **1 minut** kryssrutan och ange en **bevarande (dagar)** princip. Om du inte vill tillämpa några bevarandeprincip och behålla data alltid genom att ange **bevarande (dagar)** till **0**.
6. Klicka på **Spara**.

![Redis-diagnostik](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Utöver att arkivera dina cache mått till lagring, kan du också [strömma dem till en händelsehubb eller skicka dem till logganalys](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

Du kan visa dem i Azure portal enligt beskrivningen i den här artikeln för att komma åt dina och du kan också komma åt dem med hjälp av den [Azure övervakaren mått REST API](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

> [!NOTE]
> Om du ändrar lagringskonton data i det tidigare konfigurerade lagringskontot fortfarande är tillgänglig för nedladdning, men visas inte i Azure-portalen.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Tillgängliga mått och rapportering intervall
Cache-mått rapporteras med flera reporting intervall, inklusive **efter timme**, **idag**, **föregående vecka**, och **anpassad**. Den **mått** bladet för varje mått diagram visar de genomsnittliga, lägsta och högsta värdena för varje mått i diagrammet och vissa mått visar totalt för reporting intervall. 

Varje mått innehåller två versioner. En mätvärdet mäter prestanda för hela cachen och cacheminnen som använder [clustering](cache-how-to-premium-clustering.md), andra versionen av måttet som innehåller `(Shard 0-9)` i namnet åtgärder prestanda för en enskild Fragmentera i cache. Till exempel om en cache har 4 shards `Cache Hits` är den totala mängden träffar för hela cachen och `Cache Hits (Shard 3)` är bara träffar för att fragmentera av cachen.

> [!NOTE]
> Du kan även när cachen är inaktiv utan anslutna aktiv klient-program, för att se vissa Cacheaktivitet, till exempel anslutna klienter, minnesanvändning och åtgärder som utförs. Den här aktiviteten är normalt under driften av en Azure Redis-Cache-instans.
> 
> 

| Mått | Beskrivning |
| --- | --- |
| Cacheträffar |Antal lyckade sökningar som nyckel för under det angivna intervallet för rapportering. Detta mappas till `keyspace_hits` från Redis [INFO](http://redis.io/commands/info) kommando. |
| Cachemissar |Antal misslyckade viktiga sökningar under det angivna intervallet för rapportering. Detta mappas till `keyspace_misses` från kommandot Redis information. Cachemissar innebär inte nödvändigtvis att det finns ett problem med cacheminnet. Till exempel när du använder cache-reservera programming mönstret, ett program att leta i cacheminnet för ett objekt. Om objektet inte är det (cache-miss), är objektet hämtas från databasen och lagts till i cacheminnet för nästa gång. Cachemissar är normalt att cache-reservera programming mönstret. Om antalet cachemissar är högre än väntat, granska programlogik som fyller och läser från cacheminnet. Om objekt är att avlägsnas från cache på grund av minnesbelastning och det kan finnas vissa cachemissar, men ett bättre mått för att övervaka för minnesbelastning skulle vara `Used Memory` eller `Evicted Keys`. |
| Anslutna klienter |Antalet klientanslutningar till cache under det angivna intervallet för rapportering. Detta mappas till `connected_clients` från kommandot Redis information. En gång i [anslutningsgränsen](cache-configure.md#default-redis-server-configuration) har nått efterföljande anslutningsförsök till cache-kommer att misslyckas. Observera att även om det inte finns några aktiva klientprogram, det kan fortfarande finnas några instanser av anslutna klienter på grund av interna processer och anslutningar. |
| Avlägsnade nycklar |Antal objekt som avlägsnas från cache under det angivna reporting intervallet på grund av att den `maxmemory` gränsen. Detta mappas till `evicted_keys` från kommandot Redis information. |
| Utgångna nycklar |Antalet objekt som har upphört att gälla från cache under det angivna intervallet för rapportering. Det här värdet som mappar till `expired_keys` från kommandot Redis information. |
| Totalt antal nycklar  | Maximalt antal nycklar i cache under senaste rapporteringsperioden. Detta mappas till `keyspace` från kommandot Redis information. På grund av en begränsning i det underliggande mått-systemet för med kluster aktiverad, returnerar Totalt antal nycklar det maximala antalet nycklar Fragmentera som hade det maximala antalet nycklar under reporting intervallet.  |
| Hämtar |Antal get-åtgärder från cache under det angivna intervallet för rapportering. Det här värdet är summan av följande värden från Redis-INFO alla kommandot: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, och `cmdstat_getrange`, och motsvarar summan av träffar och missar under reporting intervallet. |
| Redis-serverbelastning |Procentandelen av cykler som Redis-servern är upptagen bearbetning och inte väntar på inaktiv för meddelanden. Om räknaren når 100 Redis-servern har uppnått ett tak för prestanda och Processorn går inte att bearbeta innebär att fungera någon snabbare. Om du ser hög Redis-serverbelastning ser du tidsgränsfel i klienten. I det här fallet bör du skala upp eller partitionering dina data till flera cacheminnen. |
| Uppsättningar |Antalet uppsättning åtgärder till cache under det angivna intervallet för rapportering. Det här värdet är summan av följande värden från Redis-INFO alla kommandot: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, och `cmdstat_setnx`. |
| Totalt antal åtgärder |Totalt antal kommandon som bearbetas av cacheserver under det angivna intervallet för rapportering. Det här värdet som mappar till `total_commands_processed` från kommandot Redis information. Observera att när Azure Redis-Cache används enbart för pub/sub det några mått för `Cache Hits`, `Cache Misses`, `Gets`, eller `Sets`, men det finns `Total Operations` mått som avspeglar cache-användning för pub/sub-åtgärder. |
| Använt minne |Mängden cacheminne som används för nyckel/värde-par i cachen i MB under det angivna intervallet för rapportering. Det här värdet som mappar till `used_memory` från kommandot Redis information. Detta inkluderar inte metadata eller fragmentering. |
| Använt minne RSS |Mängden cacheminne som används i MB under det angivna reporting intervallet, inklusive fragmentering och metadata. Det här värdet som mappar till `used_memory_rss` från kommandot Redis information. |
| Processor |Den CPU-användningen av Azure Redis-Cache-server i procent under det angivna intervallet för rapportering. Det här värdet som mappar till operativsystemet `\Processor(_Total)\% Processor Time` prestandaräknare. |
| Cacheläsning |Mängden data som har lästs från cacheminnet i megabyte per sekund (MB/s) under det angivna intervallet för rapportering. Det här värdet är härledd från nätverkskort som stöder den virtuella datorn som är värd för cachen inte och Redis specifika. **Det här värdet motsvarar nätverksbandbredden som används av det här cacheminnet. Om du vill konfigurera aviseringar för server side nätverket bandbreddsgränser sedan skapa den med den här `Cache Read` räknaren. Se [tabellen](cache-faq.md#cache-performance) för observerade bandbreddsgränser för olika cache priser nivåer och storlekar.** |
| Cacheskrivning |Mängden data som skrivs till cachen i megabyte per sekund (MB/s) under den angivna reporting intervall. Det här värdet är härledd från nätverkskort som stöder den virtuella datorn som är värd för cachen inte och Redis specifika. Det här värdet motsvarar bandbredd på data som skickas till cachen från klienten. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Aviseringar
Du kan konfigurera för att ta emot varningar baserat på mått och aktivitet. Azure-Monitor kan du konfigurera en avisering när den utlöser gör du följande:

* Skicka ett e-postmeddelande
* anropa en webhook
* Anropa en Azure Logikapp

Om du vill konfigurera Varningsregler för ditt cacheminne, klickar du på **Varna regler** från den **resurs menyn**.

![Övervakning](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Mer information om hur du konfigurerar och använder aviseringar finns [översikt över aviseringar](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Aktivitetsloggar
Aktiviteten loggarna ger information om åtgärder som utfördes på Azure Redis-Cache-instanser. Det som tidigare kallades ”granskningsloggar” eller ”arbetsloggarna”. Med aktivitetsloggar kan du bestämma den ”vad som, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas på Azure Redis-Cache-instanser. 

> [!NOTE]
> Aktivitetsloggar omfattar inte läsåtgärder (GET).
>
>

Om du vill visa aktivitetsloggar för ditt cacheminne, klickar du på **aktivitetsloggar** från den **resurs menyn**.

Läs mer om aktivitetsloggar [översikt över Azure-aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











