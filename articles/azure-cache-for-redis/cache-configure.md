---
title: Så här konfigurerar du Azure Cache för Redis | Microsoft Docs
description: Förstå Redis standardkonfigurationen för Azure Cache för Redis och lär dig hur du konfigurerar din Azure-Cache för Redis-instanser
services: azure-cache-for-redis
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: wesmc
ms.openlocfilehash: 8a78823a208a5310e62714de7b1a3cd2e35eaa8f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104683"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Så här konfigurerar du Azure Cache för Redis
Det här avsnittet beskrivs konfigurationerna som är tillgängliga för din Azure-Cache för Redis-instanser. Det här avsnittet innehåller också Redis server standardkonfigurationen för Azure Cache för Redis-instanser.

> [!NOTE]
> Läs mer om att konfigurera och använda premiumfunktioner för cache [så här konfigurerar du persistence](cache-how-to-premium-persistence.md), [konfigurera klustring](cache-how-to-premium-clustering.md), och [så här konfigurerar du Virtual Network-stöd ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurera Azure Cache för Redis-inställningar
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache för Redis-inställningar visas och konfigureras på den **Azure Cache för Redis** bladet med hjälp av den **Resursmenyn**.

![Azure Cache för Redis-inställningar](./media/cache-configure/redis-cache-settings.png)

Du kan visa och konfigurera följande inställningar med hjälp av den **Resursmenyn**.

* [Översikt](#overview)
* [Aktivitetslogg](#activity-log)
* [Åtkomstkontroll (IAM)](#access-control-iam)
* [Taggar](#tags)
* [Diagnostisera och lösa problem](#diagnose-and-solve-problems)
* [Inställningar](#settings)
    * [Åtkomstnycklar](#access-keys)
    * [Avancerade inställningar](#advanced-settings)
    * [Azure Cache för Redis Advisor](#azure-cache-for-redis-advisor)
    * [Skalning](#scale)
    * [Redis-klusterstorlek](#cluster-size)
    * [Redis-datapersistens](#redis-data-persistence)
    * [Schemauppdateringar](#schedule-updates)
    * [Geo-replikering](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Brandvägg](#firewall)
    * [Egenskaper](#properties)
    * [Lås](#locks)
    * [Automationsskript](#automation-script)
* [Administration](#administration)
    * [Importera data](#importexport)
    * [Exportera data](#importexport)
    * [Starta om](#reboot)
* [Övervakning](#monitoring)
    * [Redis-mått](#redis-metrics)
    * [Varningsregler](#alert-rules)
    * [Diagnostik](#diagnostics)
* [Support och felsökning inställningar](#support-amp-troubleshooting-settings)
    * [Resurshälsa](#resource-health)
    * [Ny supportförfrågan](#new-support-request)


## <a name="overview"></a>Översikt

**Översikt över** ger dig med grundläggande information om din cache, exempelvis namn, portar, prisnivå, och valda cache-mått.

### <a name="activity-log"></a>Aktivitetslogg

Klicka på **aktivitetsloggen** att visa åtgärder som utförs för cacheminnet. Du kan också använda filtrering för att expandera den här vyn om du vill inkludera andra resurser. Mer information om hur du arbetar med granskningsloggarna finns i [granskningsåtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md). Mer information om övervakning av Azure Cache för Redis-händelser finns i [åtgärder och aviseringar](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Åtkomstkontroll (IAM)

Den **åtkomstkontroll (IAM)** avsnittet ger stöd för rollbaserad åtkomstkontroll (RBAC i Azure-portalen). Den här konfigurationen hjälper organisationer att uppfylla sina krav för hantering av åtkomst bara och exakt. Mer information finns i [rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Taggar

Den **taggar** avsnitt hjälper dig att organisera dina resurser. Mer information finns i [med taggar för att organisera Azure-resurser](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticera och lösa problem

Klicka på **diagnostisera och lösa problem** måste anges med vanliga problem och strategier för att lösa dem.



## <a name="settings"></a>Inställningar
Den **inställningar** avsnittet kan du få åtkomst till och konfigurera följande inställningar för cacheminnet.

* [Åtkomstnycklar](#access-keys)
* [Avancerade inställningar](#advanced-settings)
* [Azure Cache för Redis Advisor](#azure-cache-for-redis-advisor)
* [Skalning](#scale)
* [Redis-klusterstorlek](#cluster-size)
* [Redis-datapersistens](#redis-data-persistence)
* [Schemauppdateringar](#schedule-updates)
* [Geo-replikering](#geo-replication)
* [Virtual Network](#virtual-network)
* [Brandvägg](#firewall)
* [Egenskaper](#properties)
* [Lås](#locks)
* [Automationsskript](#automation-script)



### <a name="access-keys"></a>Åtkomstnycklar
Klicka på **åtkomstnycklar** att visa eller återskapa åtkomstnycklarna för ditt cacheminne. De här nycklarna som används av klienter som ansluter till ditt cacheminne.

![Azure Cache för åtkomstnycklar för Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Avancerade inställningar
Följande inställningar konfigureras på den **avancerade inställningar** bladet.

* [Åtkomstportar](#access-ports)
* [Principer för minne](#memory-policies)
* [Keyspace-meddelanden (avancerade inställningar)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Åtkomstportar
Som standard är icke-SSL-åtkomst inaktiverad för nya cacheminnen. Om du vill aktivera porten utan SSL, klickar du på **nr** för **Tillåt åtkomst endast via SSL** på den **avancerade inställningar** bladet och klicka sedan på **spara**.

> [!NOTE]
> SSL-åtkomst till Azure Cache för Redis stöder TLS 1.0 som standard. Den lägsta TLS-versionen stöds kan höjas upp till TLS 1.2 om du vill med hjälp av den **minsta TLS-version** listmenyerna på den **avancerade inställningar** bladet och klicka sedan på **spara**.

![Azure Cache för Redis-Åtkomstportar](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Principer för minne
Den **princip för max. minne**, **maxmemory-reserverade**, och **maxfragmentationmemory-reserverade** inställningarna på den **avancerade inställningar** bladet konfigurera principer för minne för cachen.

![Azure Cache för Redis-princip för max. minne](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Princip för max. minne** konfigurerar avlägsningsprincipen för cachen och gör att du kan välja mellan följande avlägsningsprinciper:

* `volatile-lru` – Det här är standardprincipen för borttagning.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Mer information om `maxmemory` principer, se [avlägsningsprinciper](https://redis.io/topics/lru-cache#eviction-policies).

Den **maxmemory-reserverade** inställningen konfigurerar mängden minne i MB som har reserverats för icke-cache-åtgärder, till exempel replikering under en redundansväxling. Det här värdet kan du ha en mer konsekvent användarupplevelse för Redis-server när din belastningen varierar. Det här värdet ska anges högre för arbetsbelastningar som är skriva aktiverat. När minnet är reserverat för sådana åtgärder är inte tillgänglig för lagring av cachelagrade data.

Den **maxfragmentationmemory-reserverade** inställningen konfigurerar mängden minne i MB som är reserverade för att anpassa för minnesfragmentering. Det här värdet kan du ha en mer konsekvent användarupplevelse för Redis-server när cachen är full eller nära fullständig och fragmenteringen förhållande är hög. När minnet är reserverat för sådana åtgärder är inte tillgänglig för lagring av cachelagrade data.

En sak att tänka på när du väljer en ny reservation minnesvärdet (**maxmemory-reserverade** eller **maxfragmentationmemory-reserverade**) är hur den här ändringen kan påverka ett cacheminne som redan körs med stora mängder data i den. Till exempel om du har ett cacheminne på 53 GB med 49 GB data och sedan ändrar du värdet för reservation till 8 GB, släppa den maximala tillgängliga minnet för systemet nedåt 45 GB i den här ändringen. Om antingen din aktuella `used_memory` eller din `used_memory_rss` värden är högre än den nya gränsen på 45 GB och systemet måste du ta bort data förrän både `used_memory` och `used_memory_rss` är lägre än 45 GB. Borttagning kan öka server belastning och minne fragmentering. Mer information om cachemått, till exempel `used_memory` och `used_memory_rss`, se [tillgängliga mått och rapportering intervall](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> Den **maxmemory-reserverade** och **maxfragmentationmemory-reserverade** inställningarna är bara tillgängliga för Standard och Premium-cacheminnen.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace-meddelanden (avancerade inställningar)
Redis keyspace-meddelanden har konfigurerats på den **avancerade inställningar** bladet. Keyspace-meddelanden tillåter klienter att ta emot meddelanden när vissa händelser inträffar.

![Azure Redis-Cache avancerade inställningar](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Keyspace-meddelanden och **meddela-keyspace-händelser** inställningen är endast tillgänglig för Standard och Premium-cacheminnen.
> 
> 

Mer information finns i [Redis Keyspace-meddelanden](https://redis.io/topics/notifications). Exempelkoden finns på [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) fil i den [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplet.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache för Redis Advisor
Den **Azure Cache för Redis Advisor** visar bladet rekommendationer för cacheminnet. Inga rekommendationer visas under normal drift. 

![Rekommendationer](./media/cache-configure/redis-cache-no-recommendations.png)

Om eventuella villkor uppstå under driften av din cache som hög minnesanvändning, nätverkets bandbredd eller belastningen på servern, visas en avisering på den **Azure Cache för Redis** bladet.

![Rekommendationer](./media/cache-configure/redis-cache-recommendations-alert.png)

Mer information finns på den **rekommendationer** bladet.

![Rekommendationer](./media/cache-configure/redis-cache-recommendations.png)

Du kan övervaka de här måtten på den [övervakning diagram](cache-how-to-monitor.md#monitoring-charts) och [användning diagram](cache-how-to-monitor.md#usage-charts) delar av den **Azure Cache för Redis** bladet.

Varje prisnivå har olika begränsningar för klientanslutningar, minne och bandbredd. Om din cache närmar sig maximal kapacitet för de här måtten under en längre period, skapas en rekommendation. Mer information om mått och gränser som granskas av den **rekommendationer** verktyget, se tabellen nedan:

| Azure Cache för Redis-mått | Mer information |
| --- | --- |
| Användning av nätverksbandbredd |[Prestanda för cache - tillgänglig bandbredd](cache-faq.md#cache-performance) |
| Anslutna klienter |[Default Redis server configuration - maxclients](#maxclients) |
| Belastningen på servern |[Användning diagram – Redis-serverbelastning](cache-how-to-monitor.md#usage-charts) |
| Minnesanvändning |[Prestanda för cache - storlek](cache-faq.md#cache-performance) |

Om du vill uppgradera ditt cacheminne, klickar du på **uppgradera nu** att ändra prisnivån och [skala](#scale) din cache. Läs mer om att välja en prisnivå [vilket Azure Cache för Redis-erbjudande och vilken storlek ska jag använda?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Skala
Klicka på **skala** att visa eller ändra prisnivån för ditt cacheminne. Läs mer om att skala [så skala Azure Cache för Redis](cache-how-to-scale.md).

![Azure Cache för prisnivå](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis-klusterstorlek
Klicka på **(FÖRHANDSVERSION) Redis-klusterstorlek** ändra klusterstorlek för en löpande premium-cache med klustring aktiverat.

> [!NOTE]
> Observera att även om Azure Cache Redis Premium-nivån har släppts allmänt tillgängliga, Redis-klusterstorlek funktionen är för närvarande i förhandsversion.
> 
> 

![Redis-klusterstorlek](./media/cache-configure/redis-cache-redis-cluster-size.png)

Om du vill ändra klusterstorleken använder sedan skjutreglaget eller ange ett tal mellan 1 och 10 i den **shardantal** textrutan och klicka på **OK** att spara.

> [!IMPORTANT]
> Redis-klustring är endast tillgängligt för Premium-cache. Mer information finns i [så här konfigurerar du klustring för Premium Azure Cache för Redis](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Redis-datapersistens
Klicka på **Redis-datapersistens** för att aktivera, inaktivera eller konfigurera datapersistence för premium-cache. Azure Cache för Redis erbjuder Redis-persistens med hjälp av antingen [RDB persistence](cache-how-to-premium-persistence.md#configure-rdb-persistence) eller [AOF persistence](cache-how-to-premium-persistence.md#configure-aof-persistence).

Mer information finns i [så här konfigurerar du persistence för Premium Azure Cache för Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Redis-datapersistence är endast tillgängligt för Premium-cache.
> 
> 

### <a name="schedule-updates"></a>Schemauppdateringar
Den **Schemalägg uppdateringar** kan du ange en underhållsperiod för uppdateringar för Redis-server för cacheminnet. 

> [!IMPORTANT]
> Underhållsperioden gäller endast för att Redis serveruppdateringar, och inte till några Azure uppdaterar eller uppdateringar av operativsystemet på de virtuella datorerna som är värdar för cachen.
> 
> 

![Schemauppdateringar](./media/cache-configure/redis-schedule-updates.png)

Om du vill ange en underhållsperiod, de önskade dagarna och ange starttid för underhåll fönstret för varje dag och klicka på **OK**. Underhållsfönstertiden är i UTC.

> [!IMPORTANT]
> Den **Schemalägg uppdateringar** funktionen är bara tillgänglig för Premium-nivån cacheminnen. Mer information och instruktioner finns i [Azure Cache för administration av Redis - schemauppdateringar](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Geo-replikering

Den **Geo-replikering** bladet är en mekanism för att länka två Premium-nivån Azure Cache för Redis-instanser. En cache anges den primära länkade cachen och den andra som sekundär länkat cacheminne. Sekundär länkade cacheminnet blir skrivskyddade och data som skrivs till den primära cachen replikeras till det sekundära länkat cacheminnet. Den här funktionen kan användas för att replikera en cache i Azure-regioner.

> [!IMPORTANT]
> **GEO-replikering** är bara tillgängligt för Premium-nivån cacheminnen. Mer information och instruktioner finns i [konfigurera Geo-replikering för Azure Cache för Redis](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
Den **virtuellt nätverk** avsnittet kan du konfigurera inställningar för virtuella nätverk för cacheminnet. Stöd för information om hur du skapar en premium-cache med ett virtuellt nätverk och uppdaterar sina inställningar, se [så här konfigurerar du stöd för virtuella nätverk för Premium Azure Cache för Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Inställningar för virtuella nätverk är bara tillgängliga för premium-cacheminnen som konfigurerades med VNET-stöd när cache skapas. 
> 
> 

### <a name="firewall"></a>Brandvägg

Brandväggskonfiguration för regler är tillgänglig för alla Azure-Cache för Redis-nivåerna.

Klicka på **brandväggen** att visa och konfigurera brandväggens regler för cache.

![Brandvägg](./media/cache-configure/redis-firewall-rules.png)

Du kan ange brandväggsregler med en start- och IP-adressintervall. När brandväggsregler har konfigurerats, kan bara klientanslutningar från de angivna IP-adressintervall ansluta till cachen. När en brandväggsregel sparas finns det en kort fördröjning innan regeln gäller. Den här fördröjningen är vanligtvis mindre än en minut.

> [!IMPORTANT]
> Anslutningar från Azure Cache för Redis övervakningssystem tillåts alltid, även om brandväggsregler har konfigurerats.
> 
> 

### <a name="properties"></a>Egenskaper
Klicka på **egenskaper** att visa information om din cache, inklusive cache-slutpunkten och portar.

![Azure Cache för Redis-egenskaper](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Lås
Den **låser** avsnittet kan du låsa en prenumeration, resursgrupp eller resurs för att förhindra att andra användare i din organisation av misstag tar bort eller ändrar viktiga resurser. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Automationsskript

Klicka på **automationsskript** att skapa och exportera en mall för dina distribuerade resurser för framtida distributioner. Mer information om hur du arbetar med mallar finns i [distribuera resurser med Azure Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Administrationsinställningar för
Inställningarna i den **Administration** avsnittet kan du utföra följande administrativa uppgifter för cacheminnet. 

![Administration](./media/cache-configure/redis-cache-administration.png)

* [Importera data](#importexport)
* [Exportera data](#importexport)
* [Starta om](#reboot)


### <a name="importexport"></a>Import/Export
Import/Export är en Azure-Cache för Redis data management-åtgärd, där du kan importera och exportera data i cacheminnet genom att importera och exportera en Azure-Cache för Redis-databasen (RDB) ögonblicksbild från en premium-cache till en sidblob i ett Azure Storage-konto. Import/Export kan du migrera mellan olika Azure-Cache för Redis-instanser eller fylla i cachen med data före användning.

Import kan användas för att hämta Redis kompatibla RDB-filer från en Redis-server som kör i valfritt moln eller en miljö, inklusive Redis som körs på Linux, Windows eller någon annan molnleverantör, till exempel Amazon Web Services och andra. Importera data är ett enkelt sätt att skapa ett cacheminne med fyllda. Under importen, Azure Cache för Redis läser in RDB-filer från Azure storage i minnet och sedan infogar nycklarna i cachen.

Exportera kan du exportera de data som lagras i Azure Cache för Redis Redis kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache för Redis-instans till en annan eller till en annan Redis-server. Under exporten, skapas en temporär fil på den virtuella datorn som är värd för Azure Cache för Redis server-instansen och filen har överförts till det angivna lagringskontot. När exporten är klar med antingen en status för en lyckad eller misslyckad tas den tillfälliga filen bort.

> [!IMPORTANT]
> Import/Export är endast tillgängligt för Premium-nivån cacheminnen. Mer information och instruktioner finns i [importera och exportera data i Azure Cache för Redis](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Starta om
Den **omstart** kan du starta om noderna i ditt cacheminne. Den här funktionen för omstart kan du testa ditt program för återhämtning om det uppstår ett fel för en cachenod.

![Starta om](./media/cache-configure/redis-cache-reboot.png)

Om du har en premium-cache med klustring aktiverat kan välja du cacheskärvor ska startas om.

![Starta om](./media/cache-configure/redis-cache-reboot-cluster.png)

Starta om en eller flera noder för din cachelagring, Välj önskade noderna och sedan **omstart**. Om du har en premium-cache med klustring aktiverat, Välj fragment som ska starta om och klickar sedan på **omstart**. Efter några minuter tillbaka på valda nod(er) omstart och är online ett par minuter senare.

> [!IMPORTANT]
> Omstart är nu tillgängligt för alla prisnivåer. Mer information och instruktioner finns i [Azure Cache för administration av Redis - omstart](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Övervakning

Den **övervakning** avsnittet kan du konfigurera diagnostik och övervakning för Azure Cache för Redis. Mer information om Azure Cache för Redis övervakning och diagnostik finns [övervaka Azure Cache för Redis](cache-how-to-monitor.md).

![Diagnostik](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-mått](#redis-metrics)
* [Varningsregler](#alert-rules)
* [Diagnostik](#diagnostics)

### <a name="redis-metrics"></a>Redis-mått
Klicka på **Redis-mått** till [visa mått](cache-how-to-monitor.md#view-cache-metrics) för cacheminnet.

### <a name="alert-rules"></a>Varningsregler

Klicka på **Aviseringsregler** att konfigurera aviseringar baserat på Azure Cache för Redis-mått. Mer information finns i [aviseringar](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostik

Cache-mått i Azure Monitor är som standard [lagras i 30 dagar](../azure-monitor/platform/data-collection.md#metrics) och tas sedan bort. För att bevara din cache-mått längre än 30 dagar, klickar du på **diagnostik** till [konfigurera lagringskontot](cache-how-to-monitor.md#export-cache-metrics) används för att lagra cachediagnostik.

>[!NOTE]
>Förutom arkivering cache-mått till lagring, kan du också [strömma dem till en Event hub eller skicka dem till Log Analytics](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Support och felsökning inställningar
Inställningarna i den **Support och felsökning** avsnittet ger dig alternativ för att åtgärda problem med din cache.

![Support och felsökning](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Resurshälsa](#resource-health)
* [Ny supportförfrågan](#new-support-request)

### <a name="resource-health"></a>Resurshälsa
**Resurshälsa** övervakar resursen och meddelar dig om den körs som förväntat. Läs mer om Azure Resource health service [översikt över hälsotillståndet för Azure Resource](../resource-health/resource-health-overview.md).

> [!NOTE]
> Resource health kan för närvarande inte att rapportera om hälsotillståndet för Azure Cache för Redis-instanser i ett virtuellt nätverk. Mer information finns i [fungerar alla cache-funktioner när du har en cache i ett virtuellt nätverk?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Ny supportbegäran
Klicka på **ny supportbegäran** att öppna en supportförfrågan för cacheminnet.





## <a name="default-redis-server-configuration"></a>Standardkonfigurationen för Redis-server
Den nya Azure Cache för Redis-instanser har konfigurerats med följande Redis configuration standardvärden:

> [!NOTE]
> Inställningarna i det här avsnittet kan inte ändras med hjälp av den `StackExchange.Redis.IServer.ConfigSet` metoden. Om den här metoden anropas med något av kommandona i det här avsnittet, genereras ett undantag som liknar följande exempel:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Alla värden som kan konfigureras som **max-minne-policy**, kan konfigureras via Azure portal eller kommandoradsverktygen som Azure CLI eller PowerShell.
> 
> 

| Inställning | Standardvärde | Beskrivning |
| --- | --- | --- |
| `databases` |16 |Standardvärdet för antal databaser är 16 men du kan konfigurera ett annat antal baserat på prisnivån. <sup>1</sup> standarddatabasen är DB 0 kan du välja ett annat namn på en per anslutning med hjälp av `connection.GetDatabase(dbid)` där `dbid` är ett tal mellan `0` och `databases - 1`. |
| `maxclients` |Beror på prisnivå<sup>2</sup> |Det här värdet är det maximala antalet anslutna klienter som tillåts på samma gång. När gränsen har nåtts stängs Redis alla nya anslutningar kommer tillbaka ett högsta antal klienter nått fel. |
| `maxmemory-policy` |`volatile-lru` |Princip för max. minne är inställningen för hur Redis väljer vad du ska ta bort när `maxmemory` (storleken på den cache-erbjudande du valde när du har skapat cacheminnet) har nåtts. Med Azure Cache för Redis standardinställningen är `volatile-lru`, som tar bort nycklarna med ett förfallodatum som anges med en LRU-algoritm. Den här inställningen kan konfigureras i Azure-portalen. Mer information finns i [minne principer](#memory-policies). |
| `maxmemory-samples` |3 |Är uppskattade algoritmer i stället för exakta algoritmer för att spara minne, LRU- och minimal TTL-algoritmer. Redis kontrollerar tre nycklar och plockningar det som har använts nyligen mindre som standard. |
| `lua-time-limit` |5 000 |Maximal körningstid för ett Lua-skript i millisekunder. Om den högsta körningstiden har nåtts loggar Redis att ett skript är fortfarande i körningen efter att den längsta tillåtna tid och börjar svarar på frågor med ett fel. |
| `lua-event-limit` |500 |Maxstorlek på skriptet händelsekön. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Klienten utdata bufferten gränserna kan användas för att framtvinga frånkoppling av klienter som inte är att läsa data från servern som är tillräckligt fort av någon anledning (en vanlig orsak är att en Pub/Sub-klient inte kan använda meddelanden så snabbt som utgivaren kan producera dem). Mer information finns på [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>gränsen för `databases` är olika för varje Azure-Cache för Redis prisnivå och kan anges på Skapa cache. Om ingen `databases` inställningen anges när cache skapas standardvärdet är 16.

* Grundläggande och Standard-cache
  * C0 (250 MB) cache – upp till 16 databaser
  * C1 (1 GB) cache – upp till 16 databaser
  * C2 (2,5 GB) cache – upp till 16 databaser
  * C3 (6 GB) cache – upp till 16 databaser
  * C4 (13 GB) cache – upp till 32 databaser
  * C5 (26 GB) cache – upp till 48 databaser
  * C6 (53 GB) cache – upp till 64 databaser
* Premium-cache
  * P1 (6 GB – 60 GB) – upp till 16 databaser
  * P2 (13 GB - 130 GB) – upp till 32 databaser
  * P3 (26 GB 260 GB) – upp till 48 databaser
  * P4 (53 GB 530 GB) – upp till 64 databaser
  * Alla premium-cache med Redis-kluster som är aktiverad - Redis-kluster stöder bara användning av databas 0 så `databases` begränsa för alla premium-cache med Redis-kluster som är aktiverad är effektivt 1 och [Välj](https://redis.io/commands/select) kommandot är inte tillåtet. Mer information finns i [behöver jag göra ändringar i mitt klientprogram för att använda kluster?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Mer information om databaser finns i [vad är Redis-databaser?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> Den `databases` inställning kan vara konfigurerade under Skapa cache och endast med hjälp av PowerShell, CLI eller andra management-klienter. Ett exempel på hur du konfigurerar `databases` när cache skapas med hjälp av PowerShell, se [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` är olika för varje Azure-Cache för prisnivå.

* Grundläggande och Standard-cache
  * C0 (250 MB) cache – upp till 256 anslutningar
  * C1 (1 GB) cache – upp till 1 000 anslutningar
  * C2 (2,5 GB) cache – upp till 2 000 anslutningar
  * C3 (6 GB) cache – upp till 5 000 anslutningar
  * C4 (13 GB) cache – upp till 10 000 anslutningar
  * C5 (26 GB) cache – upp till 15 000 anslutningar
  * C6 (53 GB) cache – upp till 20 000 anslutningar
* Premium-cache
  * P1 (6 GB – 60 GB) – upp till 7 500 anslutningar
  * P2 (13 GB - 130 GB) – upp till 15 000 anslutningar
  * P3 (26 GB 260 GB) – upp till 30 000 anslutningar
  * P4 (53 GB 530 GB) – upp till 40 000 anslutningar

> [!NOTE]
> Med varje storleken på cacheminnet kan *upp till* ett visst antal anslutningar, varje anslutning till Redis är overhead kopplat till den. Ett exempel på sådana kostnader är användning av CPU och minne till följd av TLS/SSL-kryptering. Anslutningens maximala gränsen för en viss cachestorlek förutsätter en lågt belastade cache. Om läsa in från anslutningen overhead *plus* belastningen från Klientåtgärder överskrider kapaciteten för systemet, cacheminnet kan uppleva kapacitetsproblem även om du inte har överskridit anslutningsgränsen för den aktuella cachestorleken.
> 
> 



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis-kommandon som inte stöds i Azure Cache för Redis
> [!IMPORTANT]
> Eftersom konfiguration och hantering av Azure Cache för Redis-instanser hanteras av Microsoft, inaktiveras följande kommandon. Om du försöker anropa dem du får ett felmeddelande som liknar `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * FELSÖKA
> * MIGRERA
> * Spara
> * STÄNG AV
> * SLAVEOF
> * KLUSTER - kluster skrivna kommandon är inaktiverade, men skrivskyddade kluster kommandon tillåts.
> 
> 

Mer information om Redis-kommandon finns i [ https://redis.io/commands ](https://redis.io/commands).

## <a name="redis-console"></a>Redis-konsol
Du kan på ett säkert sätt skicka kommandon till din Azure-Cache för Redis-instanser med hjälp av den **Redis-konsolen**, som är tillgängligt i Azure-portalen för alla nivåer av cache.

> [!IMPORTANT]
> - Redis-konsolen fungerar inte med [VNET](cache-how-to-premium-vnet.md). När din cache är en del av ett virtuellt nätverk kan endast klienter i det virtuella nätverket kan komma åt cachen. Eftersom Redis-konsolen körs i din lokala webbläsare, vilket är utanför det virtuella nätverket, kan den inte kan ansluta till ditt cacheminne.
> - Inte alla Redis-kommandon stöds i Azure Cache för Redis. En lista över Redis-kommandon som är inaktiverade för Azure Cache för Redis, finns i den tidigare [Redis-kommandon som inte stöds i Azure Cache för Redis](#redis-commands-not-supported-in-azure-cache-for-redis) avsnittet. Mer information om Redis-kommandon finns i [ https://redis.io/commands ](https://redis.io/commands).
> 
> 

Om du vill ansluta till Redis-konsolen klickar du på **konsolen** från den **Azure Cache för Redis** bladet.

![Redis-konsol](./media/cache-configure/redis-console-menu.png)

Skriv kommandot önskade i konsolen för att utfärda kommandon mot din cache-instans.

![Redis-konsol](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Använda Redis-konsolen med premium klustrade cache

När du använder Redis-konsolen med en premium klustrad cache, kan du skicka kommandon till en enda shard cache. För att utfärda ett kommando till en specifik shard först ansluta till det önskade fragmentet genom att klicka på den på väljaren fragment.

![Redis-konsol](./media/cache-configure/redis-console-premium-cluster.png)

Om du försöker få åtkomst till en nyckel som lagras i en annan shard än ansluten shard, visas ett felmeddelande som liknar följande meddelande:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

I exemplet ovan shard 1 är det valda fragmentet men `myKey` finns i fragment 0, som anges av den `(shard 0)` delen av ett felmeddelande. I det här exemplet att komma åt `myKey`fragment 0 med hjälp av Väljaren för fragment och välj sedan utfärda kommandot önskade.


## <a name="move-your-cache-to-a-new-subscription"></a>Flytta ditt cacheminne till en ny prenumeration
Du kan flytta ditt cacheminne till en ny prenumeration genom att klicka på **flytta**.

![Flytta Azure Cache för Redis](./media/cache-configure/redis-cache-move.png)

Information om hur du flyttar resurser från en resursgrupp till en annan och från en prenumeration till en annan finns i [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du arbetar med Redis-kommandon finns i [hur kan jag köra Redis-kommandon?](cache-faq.md#how-can-i-run-redis-commands)

