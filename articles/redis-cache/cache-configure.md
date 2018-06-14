---
title: Så här konfigurerar du Azure Redis-Cache | Microsoft Docs
description: Förstå Redis standardkonfigurationen för Azure Redis-Cache och lära dig hur du konfigurerar Azure Redis-Cache-instanser
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: wesmc
ms.openlocfilehash: 0cd21c0367a95d3e866137797ac32fc5bdd196c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31420744"
---
# <a name="how-to-configure-azure-redis-cache"></a>Så här konfigurerar du Azure Redis-Cache
Det här avsnittet beskrivs konfigurationerna som är tillgängliga för Azure Redis-Cache-instanser. Det här avsnittet beskrivs även standardkonfigurationen för Redis-servern för Azure Redis-Cache-instanser.

> [!NOTE]
> Mer information om hur du konfigurerar och använder premium cache-funktioner finns [hur du konfigurerar persistence](cache-how-to-premium-persistence.md), [konfigurera klustring](cache-how-to-premium-clustering.md), och [så här konfigurerar du stöd för virtuella nätverk ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Konfigurera inställningar för Redis-cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Redis-Cache-inställningar visas och konfigureras på den **Redis-Cache** bladet med hjälp av den **resurs menyn**.

![Redis-Cache-inställningar](./media/cache-configure/redis-cache-settings.png)

Du kan visa och konfigurera följande inställningar med hjälp av den **resurs menyn**.

* [Översikt](#overview)
* [Aktivitetslogg](#activity-log)
* [Åtkomstkontroll (IAM)](#access-control-iam)
* [Taggar](#tags)
* [Diagnostisera och lösa problem](#diagnose-and-solve-problems)
* [Inställningar](#settings)
    * [Snabbtangenter](#access-keys)
    * [Avancerade inställningar](#advanced-settings)
    * [Redis-Cache Advisor](#redis-cache-advisor)
    * [Skalning](#scale)
    * [Redis klusterstorleken](#cluster-size)
    * [Redis-datapersistence](#redis-data-persistence)
    * [Schemauppdateringar](#schedule-updates)
    * [Geo-replikering](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Brandvägg](#firewall)
    * [Egenskaper](#properties)
    * [Lås](#locks)
    * [Automation-skript](#automation-script)
* [Administration](#administration)
    * [Importera data](#importexport)
    * [Exportera data](#importexport)
    * [Starta om](#reboot)
* [Övervakning](#monitoring)
    * [Redis-mått](#redis-metrics)
    * [Aviseringsregler](#alert-rules)
    * [Diagnostik](#diagnostics)
* [Support och felsökning av inställningar](#support-amp-troubleshooting-settings)
    * [Resurshälsa](#resource-health)
    * [ny supportbegäran](#new-support-request)


## <a name="overview"></a>Översikt

**Översikt över** ger dig med grundläggande information om ditt cacheminne, till exempel namn, portar, prisnivå, och valda cache mått.

### <a name="activity-log"></a>Aktivitetslogg

Klicka på **aktivitetsloggen** att visa åtgärder som utförs på ditt cacheminne. Du kan också använda filtrering för att expandera den här vyn om du vill inkludera andra resurser. Mer information om hur du arbetar med granskningsloggarna finns [granskningsåtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md). Mer information om hur du övervakar Azure Redis-Cache-händelser finns [åtgärder och aviseringar](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Åtkomstkontroll (IAM)

Den **åtkomstkontroll (IAM)** avsnittet ger stöd för rollbaserad åtkomstkontroll (RBAC) i Azure-portalen. Den här konfigurationen hjälper organisationer att uppfylla krav deras åtkomst bara och exakt. Mer information finns i [rollbaserad åtkomstkontroll i Azure portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Taggar

Den **taggar** avsnitt kan du ordna dina resurser. Mer information finns i [med taggar för att organisera dina Azure-resurser](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticera och lösa problem

Klicka på **diagnostisera och lösa problem** måste anges med vanliga problem och strategier för att lösa dem.



## <a name="settings"></a>Inställningar
Den **inställningar** avsnittet kan du komma åt och konfigurera följande inställningar för ditt cacheminne.

* [Snabbtangenter](#access-keys)
* [Avancerade inställningar](#advanced-settings)
* [Redis-Cache Advisor](#redis-cache-advisor)
* [Skalning](#scale)
* [Redis klusterstorleken](#cluster-size)
* [Redis-datapersistence](#redis-data-persistence)
* [Schemauppdateringar](#schedule-updates)
* [Geo-replikering](#geo-replication)
* [Virtual Network](#virtual-network)
* [Brandvägg](#firewall)
* [Egenskaper](#properties)
* [Lås](#locks)
* [Automation-skript](#automation-script)



### <a name="access-keys"></a>Åtkomstnycklar
Klicka på **åtkomstnycklar** att visa eller återskapa åtkomstnycklarna för ditt cacheminne. De här nycklarna som används av klienter som ansluter till ditt cacheminne.

![Redis-Cache snabbtangenter](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Avancerade inställningar
Följande inställningar konfigureras på den **avancerade inställningar** bladet.

* [Åtkomstportar](#access-ports)
* [Principer för minne](#memory-policies)
* [Keyspace-meddelanden (avancerade inställningar)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Åtkomstportar
Som standard är icke-SSL-åtkomst inaktiverad för nya cacheminnen. Om du vill aktivera porten utan SSL **nr** för **Tillåt åtkomst endast via SSL** på den **avancerade inställningar** bladet och klicka sedan på **spara**.

![Åtkomstportar för redis-Cache](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Principer för minne
Den **Maxmemory princip**, **maxmemory-reserverade**, och **maxfragmentationmemory-reserverade** inställningarna på den **avancerade inställningar** bladet konfigurera principer för minne för cachen.

![Redis-Cache Maxmemory princip](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory princip** konfigurerar av principen för cachen och gör att du kan välja mellan följande av principer:

* `volatile-lru` -Detta är standardprincipen för borttagning.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Mer information om `maxmemory` principer, se [av principer](http://redis.io/topics/lru-cache#eviction-policies).

Den **maxmemory-reserverade** inställningen konfigurerar mängden minne i MB som har reserverats för icke-cache-åtgärder, till exempel replikering under växling vid fel. Det här värdet kan du ha en mer konsekvent användarupplevelse för Redis-servern när din belastningen varierar. Det här värdet ska anges för arbetsbelastningar som är aktiverat. När minnet reserveras för dessa åtgärder är inte tillgänglig för lagring av cachelagrade data.

Den **maxfragmentationmemory-reserverade** inställningen konfigurerar hur mycket minne i MB som är reserverade för att anpassa för minnesfragmentering. Det här värdet kan du ha en mer konsekvent användarupplevelse för Redis-servern när cachen är full eller nära full och fragmenteringen förhållandet är hög. När minnet reserveras för dessa åtgärder är inte tillgänglig för lagring av cachelagrade data.

En sak att tänka på när du väljer ett nytt minne reservation värde (**maxmemory-reserverade** eller **maxfragmentationmemory-reserverade**) är hur den här ändringen kan påverka en cache som redan körs med stora mängder data. Till exempel om du har en 53 GB cache med 49 GB data och sedan ändra värdet för reservation till 8 GB, släppa max tillgängligt minne i systemet till 45 GB i den här ändringen. Om din aktuella `used_memory` eller `used_memory_rss` värden är högre än den nya gränsen på 45 GB och systemet måste ta bort data förrän både `used_memory` och `used_memory_rss` är lägre än 45 GB. Borttagning kan öka belastningen och minne fragmentering på servern. Mer information om cache mått som `used_memory` och `used_memory_rss`, se [tillgängliga mått och rapportering intervall](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> Den **maxmemory-reserverade** och **maxfragmentationmemory-reserverade** inställningarna är bara tillgängliga för Standard och Premium cachelagrar.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace-meddelanden (avancerade inställningar)
Redis keyspace meddelanden har konfigurerats på den **avancerade inställningar** bladet. Keyspace-meddelanden tillåter klienter att ta emot meddelanden när vissa händelser inträffar.

![Redis-Cache avancerade inställningar](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Keyspace-meddelanden och **meddela-keyspace-händelser** inställningen är bara tillgängliga för Standard- och Premium.
> 
> 

Mer information finns i [Redis Keyspace-meddelanden](http://redis.io/topics/notifications). Exempelkod finns i [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) filen i den [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exempel.


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis-cache Advisor
Den **Redis-Cache Advisor** bladet visar rekommendationer för ditt cacheminne. Inga rekommendationer visas under normal drift. 

![Rekommendationer](./media/cache-configure/redis-cache-no-recommendations.png)

Om eventuella villkor som uppstår under drift av ditt cacheminne som hög minnesanvändning, nätverkets bandbredd eller belastningen på servern, en varning visas på den **Redis-Cache** bladet.

![Rekommendationer](./media/cache-configure/redis-cache-recommendations-alert.png)

Mer information finns på den **rekommendationer** bladet.

![Rekommendationer](./media/cache-configure/redis-cache-recommendations.png)

Du kan övervaka de här måtten på den [övervakning diagram](cache-how-to-monitor.md#monitoring-charts) och [användning diagram](cache-how-to-monitor.md#usage-charts) avsnitt i den **Redis-Cache** bladet.

Varje prisnivå har olika begränsningar för klientanslutningar, minne och bandbredd. Om ditt cacheminne närmar sig maximal kapacitet för de här måtten över en längre period, skapas en rekommendation. Mer information om de mått och gränser som granskas av den **rekommendationer** verktyget, se tabellen nedan:

| Redis-Cache-mått | Mer information |
| --- | --- |
| Användningen av nätverksbandbredd |[Prestanda för cache - bandbredd](cache-faq.md#cache-performance) |
| Anslutna klienter |[Server för standardkonfigurationen - Redis-maxclients](#maxclients) |
| Belastningen på servern |[Användning-diagram - Redis-serverbelastning](cache-how-to-monitor.md#usage-charts) |
| Minnesanvändning |[Prestanda för cache - storleken](cache-faq.md#cache-performance) |

Om du vill uppgradera ditt cacheminne, klickar du på **uppgradera nu** att ändra prisnivå och [skala](#scale) ditt cacheminne. Mer information om hur du väljer en prisnivå finns [vilka Redis-Cache erbjudande och vilken storlek ska jag använda?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="scale"></a>Skala
Klicka på **skala** att visa eller ändra prisnivå för din cache. Läs mer om att skala [så skala Azure Redis-Cache](cache-how-to-scale.md).

![Redis-Cache prisnivån](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis klusterstorleken
Klicka på **(FÖRHANDSGRANSKNING) Redis klusterstorleken** ändra klusterstorleken för ett premium-cache med aktiverad klustring.

> [!NOTE]
> Observera att medan Azure Redis Cache Premium-nivån har släppts för allmän tillgänglighet, funktionen Redis Klusterstorleken är för närvarande under förhandsgranskning.
> 
> 

![Redis klusterstorleken](./media/cache-configure/redis-cache-redis-cluster-size.png)

Om du vill ändra klusterstorleken skjutreglaget eller ange ett tal mellan 1 och 10 i den **Fragmentera antal** textrutan och klicka på **OK** att spara.

> [!IMPORTANT]
> Redis-kluster är endast tillgängligt för Premium. Mer information finns i [Konfigurera klustring för premium Azure Redis-cache](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Redis-datapersistens
Klicka på **Redis-datapersistence** för att aktivera, inaktivera eller konfigurera datapersistence för premium-cache. Azure Redis-Cache erbjuder Redis-datapersistence med antingen [RDB beständiga](cache-how-to-premium-persistence.md#configure-rdb-persistence) eller [AOF beständiga](cache-how-to-premium-persistence.md#configure-aof-persistence).

Mer information finns i [hur du konfigurerar persistence för Premium Azure Redis-Cache](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Redis-datapersistence är endast tillgängligt för Premium. 
> 
> 

### <a name="schedule-updates"></a>Schemauppdateringar
Den **schemalägga uppdateringar** bladet låter dig ange en underhållsperiod för Redis-serveruppdateringar för ditt cacheminne. 

> [!IMPORTANT]
> Underhållsfönstret gäller endast för Redis-serveruppdateringar och inte till någon Azure-uppdateringar eller uppdateringar för operativsystemet på de virtuella datorerna som är värdar för cachen.
> 
> 

![Schemauppdateringar](./media/cache-configure/redis-schedule-updates.png)

Om du vill ange en underhållsperiod de önskade dagarna och ange Starttimme för underhåll fönster för varje dag och klickar på **OK**. Observera att fönstret Underhåll i UTC. 

> [!IMPORTANT]
> Den **schemalägga uppdateringar** funktioner är endast tillgängligt för Premium-nivån. Mer information och instruktioner finns i [Azure Redis-Cache administration - Schemalägg uppdateringar](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Geo-replikering

Den **georeplikering** bladet tillhandahåller en mekanism för att länka två instanser i Premium-nivån Azure Redis-Cache. En cache betecknas som primär länkade cacheminnet och andra som sekundär länkade cache. Sekundär länkade cachen skrivskyddas och data som skrivs till den primära cachen replikeras till sekundär länkade cachen. Den här funktionen kan användas för att replikera en cache över Azure-regioner.

> [!IMPORTANT]
> **GEO-replikering** är endast tillgängligt för Premium-nivån. Mer information och instruktioner finns i [hur du konfigurerar Geo-replikering för Azure Redis-Cache](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
Den **virtuellt nätverk** avsnitt kan du konfigurera inställningar för virtuella nätverk för ditt cacheminne. Information om hur du skapar en premium-cache med VNET stöd och uppdaterar sina inställningar Se [hur du konfigurerar virtuella nätverket har stöd för Premium Azure Redis-Cache](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Inställningarna för virtuella nätverk är bara tillgängliga för premium som har konfigurerats med stöd för virtuellt nätverk under skapande av cachen. 
> 
> 

### <a name="firewall"></a>Brandvägg

Regler för brandväggskonfigurationen är tillgänglig för alla nivåer för Azure Redis-Cache.

Klicka på **brandväggen** att visa och konfigurera brandväggsregler för cachen.

![Brandvägg](./media/cache-configure/redis-firewall-rules.png)

Du kan ange brandväggsregler med en start- och IP-adressintervall. Om brandväggens regler är konfigurerade kan bara klientanslutningar från de angivna IP-adressintervall ansluta till cacheminnet. När en brandväggsregel sparas, är det en kort fördröjning innan regeln börjar gälla. Den här fördröjningen är vanligtvis mindre än en minut.

> [!IMPORTANT]
> Anslutningar från Azure Redis-Cache övervakningssystem tillåts alltid, även om brandväggens regler är konfigurerade.
> 
> 

### <a name="properties"></a>Egenskaper
Klicka på **egenskaper** att visa information om ditt cacheminne, inklusive cache-slutpunkten och portar.

![Redis-Cache-egenskaper](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Lås
Den **låser** avsnitt kan du låsa en prenumeration, resursgrupp eller resurs för att förhindra andra användare i din organisation av misstag tas bort eller ändra viktiga resurser. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Automationsskript

Klicka på **automatiseringsskriptet** att skapa och exportera en mall för dina distribuerade resurser för framtida distributioner. Mer information om hur du arbetar med mallar finns [distribuera resurser med Azure Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Inställningar för administration
Inställningarna i den **Administration** avsnitt kan du utföra följande administrativa uppgifter för ditt cacheminne. 

![Administration](./media/cache-configure/redis-cache-administration.png)

* [Importera data](#importexport)
* [Exportera data](#importexport)
* [Starta om](#reboot)


### <a name="importexport"></a>Import/Export
Importera och exportera är en Azure Redis-Cache data management-åtgärd, där du kan importera och exportera data i cacheminnet genom att importera och exportera en ögonblicksbild för Redis-Cache databasen (RDB) från cache premium till en sidblob i ett Azure Storage-konto. Import/Export kan du migrera mellan olika instanser av Azure Redis-Cache eller fylla i cachen med data innan de används.

Import kan användas för att Redis kompatibel RDB filer från en Redis-server som kör i molnet eller miljö, inklusive Redis som körs på Linux, Windows eller valfri provider som molntjänster, till exempel Amazon Web Services och andra. Importera data är ett enkelt sätt att skapa ett cacheminne med data i förväg. Azure Redis-Cache under importen, laddar RDB-filer från Azure storage i minnet och infogar nycklarna i cacheminnet.

Exportera kan du exportera de data som lagras i Azure Redis-Cache för Redis-kompatibel RDB filer. Du kan använda den här funktionen för att flytta data från en Azure Redis-Cache-instans till en annan eller till en annan Redis-server. Skapa en tillfällig fil på den virtuella datorn som är värd för Azure Redis-Cache server-instansen under exporten, och överföra filen till det angivna lagringskontot. När exporten har slutförts med antingen en status för lyckad eller misslyckad, tas den temporära filen bort.

> [!IMPORTANT]
> Import/Export är endast tillgängligt för Premium-nivån. Mer information och instruktioner finns i [importera och exportera data i Azure Redis-Cache](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Starta om
Den **omstart** bladet kan du starta om noderna i ditt cacheminne. Den här funktionen för omstart kan du testa ditt program för återhämtning om det finns ett fel i en cachenod.

![Starta om](./media/cache-configure/redis-cache-reboot.png)

Om du har en premium-cache med aktiverad klustring, kan du välja vilka delar av cacheminnet för att starta om.

![Starta om](./media/cache-configure/redis-cache-reboot-cluster.png)

Starta om en eller flera noder i ditt cacheminne, Välj önskade noder och sedan **omstart**. Om du har en premium-cache med aktiverad klustring, Välj shard(s) att starta om och klickar sedan på **omstart**. Efter några minuter tillbaka på valda noder omstart och är online några minuter senare.

> [!IMPORTANT]
> Starta om datorn är nu tillgänglig för alla prisnivåer. Mer information och instruktioner finns i [Azure Redis-Cache administration - omstart](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Övervakning

Den **övervakning** avsnitt kan du konfigurera diagnostik- och övervakning för Redis-Cache. Mer information om övervakning av Azure Redis-Cache och diagnostik finns [hur du övervakar Azure Redis-Cache](cache-how-to-monitor.md).

![Diagnostik](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-mått](#redis-metrics)
* [Aviseringsregler](#alert-rules)
* [Diagnostik](#diagnostics)

### <a name="redis-metrics"></a>Redis-mått
Klicka på **Redis mått** till [visa mått](cache-how-to-monitor.md#view-cache-metrics) för ditt cacheminne.

### <a name="alert-rules"></a>Varningsregler

Klicka på **Varna regler** att konfigurera aviseringar baserat på Redis-Cache mått. Mer information finns i [aviseringar](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostik

Som standard cache i Azure-Monitor är [lagras i 30 dagar](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) och sedan tas bort. För att bevara din cache-mätvärden för längre än 30 dagar, klickar du på **diagnostik** till [konfigurera lagringskontot](cache-how-to-monitor.md#export-cache-metrics) används för att lagra cache diagnostik.

>[!NOTE]
>Utöver att arkivera dina cache mått till lagring, kan du också [strömma dem till en händelsehubb eller skicka dem till logganalys](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

## <a name="support--troubleshooting-settings"></a>Support och felsökning av inställningar
Inställningarna i den **stöd + felsökning** avsnittet förse dig med alternativ för att lösa problem med ditt cacheminne.

![Support och felsökning](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Resurshälsa](#resource-health)
* [ny supportbegäran](#new-support-request)

### <a name="resource-health"></a>Resurshälsa
**Resurshälsa** bevakar resurs och anger om den körs som förväntat. Läs mer om tjänsten för hälsotillstånd Azure-resurshanteraren [Azure Resource health översikt](../resource-health/resource-health-overview.md).

> [!NOTE]
> Resurshälsotillståndet är för närvarande inte att rapportera om hälsan för Azure Redis-Cache-instanser som finns i ett virtuellt nätverk. Mer information finns i [fungerar alla cachefunktioner när värd en cachelagring i ett virtuellt nätverk?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Ny supportbegäran
Klicka på **ny supportbegäran** att öppna en supportbegäran för ditt cacheminne.





## <a name="default-redis-server-configuration"></a>Standardkonfigurationen för Redis-server
Nya Azure Redis-Cache-instanserna är konfigurerade med följande Redis configuration standardvärden:

> [!NOTE]
> Det går inte att ändra inställningarna i det här avsnittet med hjälp av den `StackExchange.Redis.IServer.ConfigSet` metoden. Om den här metoden anropas med något av kommandona i det här avsnittet, genereras ett undantag som liknar följande exempel:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Alla värden som kan konfigureras som **maximalt minne princip**, kan konfigureras via Azure-portalen eller kommandoradsverktyget hanteringsverktyg, till exempel Azure CLI eller PowerShell.
> 
> 

| Inställning | Standardvärde | Beskrivning |
| --- | --- | --- |
| `databases` |16 |Standardantalet databaser som är 16 men du kan konfigurera ett annat antal baserat på prisnivån. <sup>1</sup> standarddatabasen är DB 0 kan du välja ett annat namn på en per anslutning grunden med hjälp av `connection.GetDatabase(dbid)` där `dbid` är ett tal mellan `0` och `databases - 1`. |
| `maxclients` |Beror på prisnivån<sup>2</sup> |Det här värdet är det maximala antalet anslutna klienter tillåts samtidigt. Gränsen har nåtts stängs Redis när alla nya anslutningar, returnera ett högsta antal klienter uppnåtts. |
| `maxmemory-policy` |`volatile-lru` |Maxmemory principen är inställningen för hur Redis väljs vad du vill ta bort när `maxmemory` (storleken på cacheminnet erbjuder du valde när du skapade cachen) har nåtts. Med Azure Redis-Cache standardinställningen är `volatile-lru`, som tar bort nycklarna med ett förfallodatum som anges med en LRU-algoritm. Den här inställningen kan konfigureras i Azure-portalen. Mer information finns i [minne principer](#memory-policies). |
| `maxmemory-samples` |3 |Om du vill spara minne är LRU- och minimal TTL-algoritmer uppskattade algoritmer istället för exakt algoritmer. Som standard Redis kontrollerar tre nycklar och plockningar som har använts nyligen mindre. |
| `lua-time-limit` |5 000 |Maximal körningstid för skript Lua i millisekunder. Om den maximala körningstiden uppnås loggar Redis att ett skript är fortfarande i körningen efter att den maximala tillåtna tiden och börjar att besvara frågor med ett fel. |
| `lua-event-limit` |500 |Maxstorlek på skriptet händelsekön. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Klienten utdata buffert gränser kan användas för att framtvinga frånkoppling av klienter som inte är att läsa data från servern tillräckligt snabbt av någon anledning (en vanlig orsak är att en Pub/Sub-klient inte kan använda meddelanden så snabbt utgivaren kan ge dem). Mer information finns på [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>gränsen för `databases` är olika för varje Azure Redis-Cache prisnivån och kan anges på Skapa cache. Om inget `databases` inställningen anges under skapande av cache standardvärdet är 16.

* Basic och Standard cacheminnen
  * C0 (250 MB) cache - upp till 16 databaser
  * C1 (1 GB)-cache - upp till 16 databaser
  * C2 (2,5 GB)-cache - upp till 16 databaser
  * C3 (6 GB)-cache - upp till 16 databaser
  * C4 (13 GB)-cache - upp till 32-databaser
  * C5 (26 GB)-cache - upp till 48 databaser
  * C6 (53 GB)-cache - upp till 64-databaser
* Premium-cache
  * P1 (6 GB - 60 GB) - upp till 16 databaser
  * P2 (13 GB - 130 GB) - upp till 32-databaser
  * P3 (26 GB - 260 GB) - upp till 48 databaser
  * P4 (53 GB - 530 GB) - upp till 64-databaser
  * Alla premium cacheminnen med Redis cluster aktiverat – Redis cluster stöder bara användning av databas 0 därför `databases` begränsa för alla premium-cache med Redis cluster aktiverat effektivt är 1 och [Välj](http://redis.io/commands/select) kommandot är inte tillåtet. Mer information finns i [behöver jag göra ändringar i client-program att använda kluster?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Mer information om databaser finns [vad är Redis databaser?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> Den `databases` inställning kan vara konfigurerade endast under Skapa cache och endast med hjälp av PowerShell, CLI eller annan av hanteringsklienter. Ett exempel på hur du konfigurerar `databases` under Skapa cache med hjälp av PowerShell, se [ny AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` är olika för varje Azure Redis-Cache prisnivån.

* Basic och Standard cacheminnen
  * C0 (250 MB) cache - upp till 256-anslutningar
  * C1 (1 GB)-cache - upp till 1 000 anslutningar
  * C2 (2,5 GB)-cache - upp till 2 000 anslutningar
  * C3 (6 GB)-cache - upp till 5 000 anslutningar
  * C4 (13 GB)-cache - upp till 10 000-anslutningar
  * C5 (26 GB)-cache - upp till 15 000 anslutningar
  * C6 (53 GB)-cache - upp till 20 000 anslutningar
* Premium-cache
  * P1 (6 GB - 60 GB) - upp till 7 500-anslutningar
  * P2 (13 GB - 130 GB) - upp till 15 000 anslutningar
  * P3 (26 GB - 260 GB) - upp till 30 000 anslutningar
  * P4 (53 GB - 530 GB) - upp till 40 000 anslutningar

> [!NOTE]
> Med varje cachens storlek kan *upp till* ett visst antal anslutningar, varje anslutning till Redis har associeras med den. Ett exempel på sådana kostnader är processor- och minnesanvändning på grund av TLS/SSL-kryptering. Anslutningens maximala gränsen för en given cachestorlek förutsätter en lågt belastade cache. Om att läsa in från anslutningen kostnader *plus* belastningen från Klientåtgärder överskrider kapaciteten för systemet, cachen kan ha kapacitet problem, även om du inte har överskridit gränsen för antal anslutningar för den aktuella cachestorleken.
> 
> 



## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis-kommandon som inte stöds i Azure Redis-Cache
> [!IMPORTANT]
> Eftersom konfiguration och hantering av Azure Redis-Cache instanser hanteras av Microsoft, inaktiveras följande kommandon. Om du försöker anropa dem du ett felmeddelande liknande `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * FELSÖKA
> * MIGRERA
> * SPARA
> * AVSTÄNGNING
> * SLAVEOF
> * KLUSTER - kluster skriva kommandon inaktiveras, men skrivskyddad klustret kommandon tillåts.
> 
> 

Mer information om Redis-kommandon finns [ http://redis.io/commands ](http://redis.io/commands).

## <a name="redis-console"></a>Redis-konsolen
På ett säkert sätt kan du skicka kommandon till din Azure Redis-Cache-instanser som använder den **Redis-konsolen**, som är tillgänglig i Azure-portalen för alla nivåer i cacheminnet.

> [!IMPORTANT]
> - Redis-konsolen fungerar inte med [VNET](cache-how-to-premium-vnet.md). När din cache är en del av ett virtuellt nätverk kan bara klienter i virtuella nätverk kan komma åt cachen. Eftersom Redis-konsolen körs i din lokala webbläsare som är utanför det virtuella nätverket, kan inte den ansluta till ditt cacheminne.
> - Inte alla Redis-kommandon stöds i Azure Redis-Cache. En lista över Redis-kommandon som är inaktiverat för Azure Redis-Cache, finns i den tidigare [Redis-kommandon som inte stöds i Azure Redis-Cache](#redis-commands-not-supported-in-azure-redis-cache) avsnitt. Mer information om Redis-kommandon finns [ http://redis.io/commands ](http://redis.io/commands).
> 
> 

Om du vill ansluta till Redis-konsolen klickar du på **konsolen** från den **Redis-Cache** bladet.

![Redis-konsolen](./media/cache-configure/redis-console-menu.png)

Skriv kommandot önskade i konsolen för att utfärda kommandon mot din cache-instans.

![Redis-konsolen](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Med premium klustrade cache Redis-konsolen

När cache med Redis-konsolen med en premium för, kan du skicka kommandon till en enda Fragmentera av cachen. Om du vill utfärda ett kommando till en specifik Fragmentera först ansluta till önskad Fragmentera genom att klicka på väljaren Fragmentera.

![Redis-konsolen](./media/cache-configure/redis-console-premium-cluster.png)

Om du försöker komma åt en nyckel som lagras i en annan Fragmentera än anslutna Fragmentera, visas ett felmeddelande som liknar följande meddelande:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

I exemplet ovan är Fragmentera 1 valda Fragmentera men `myKey` finns i Fragmentera 0, vilket indikeras med den `(shard 0)` delen av ett felmeddelande. I det här exemplet att komma åt `myKey`Fragmentera 0 med väljaren Fragmentera och välj sedan utfärda kommandot önskade.


## <a name="move-your-cache-to-a-new-subscription"></a>Flytta ditt cacheminne till en ny prenumeration
Du kan flytta ditt cacheminne till en ny prenumeration genom att klicka på **flytta**.

![Flytta Redis-Cache](./media/cache-configure/redis-cache-move.png)

Information om hur du flyttar resurser från en resursgrupp till en annan och från en prenumeration finns [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du arbetar med Redis-kommandon finns [hur kan jag köra Redis kommandon?](cache-faq.md#how-can-i-run-redis-commands)

