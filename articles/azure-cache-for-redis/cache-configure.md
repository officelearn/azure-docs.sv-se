---
title: Konfigurera Azure Cache för Redis
description: Förstå standardkonfigurationen för Redis för Azure Cache för Redis och lär dig hur du konfigurerar dina Azure-cache för Redis-instanser
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 3f0de52782694e6cbc8fdb6b55d545191dbbb350
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010315"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Konfigurera Azure Cache för Redis
I det här avsnittet beskrivs de konfigurationer som är tillgängliga för dina Azure-cache för Redis-instanser. Det här avsnittet täcker även standardkonfigurationen för Redis-server för Azure Cache för Redis-instanser.

> [!NOTE]
> Mer information om hur du konfigurerar och använder premiumcachefunktioner finns i Så här konfigurerar du [beständighet,](cache-how-to-premium-persistence.md) [Hur du konfigurerar kluster](cache-how-to-premium-clustering.md)och [konfigurerar stöd för virtuellt nätverk](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurera Azure-cache för Redis-inställningar
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache för Redis-inställningar visas och konfigureras på **Azure Cache for Redis-bladet** med hjälp av **resursmenyn**.

![Azure Cache för Redis-inställningar](./media/cache-configure/redis-cache-settings.png)

Du kan visa och konfigurera följande inställningar med hjälp av **Resursmenyn**.

* [Översikt](#overview)
* [Aktivitetslogg](#activity-log)
* [Åtkomstkontroll (IAM)](#access-control-iam)
* [Taggar](#tags)
* [Diagnostisera och lösa problem](#diagnose-and-solve-problems)
* [Inställningar](#settings)
    * [Åtkomstnycklar](#access-keys)
    * [Avancerade inställningar](#advanced-settings)
    * [Azure Cache för Redis Advisor](#azure-cache-for-redis-advisor)
    * [Skala](#scale)
    * [Klusterstorlek](#cluster-size)
    * [Data persistens](#redis-data-persistence)
    * [Schemauppdateringar](#schedule-updates)
    * [Geo-replikering](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Brandvägg](#firewall)
    * [Egenskaper](#properties)
    * [Lås](#locks)
    * [Automationsskript](#automation-script)
* Administration
    * [Importera data](#importexport)
    * [Exportera data](#importexport)
    * [Starta om](#reboot)
* [Övervakning](#monitoring)
    * [Redis-mått](#redis-metrics)
    * [Varningsregler](#alert-rules)
    * [Diagnostik](#diagnostics)
* Stöd & felsökningsinställningar
    * [Resurshälsa](#resource-health)
    * [Ny supportbegäran](#new-support-request)


## <a name="overview"></a>Översikt

**Översikt** ger dig grundläggande information om cacheminnet, till exempel namn, portar, prisnivå och valda cachemått.

### <a name="activity-log"></a>Aktivitetslogg

Klicka på **Aktivitetslogg** om du vill visa åtgärder som utförs på cacheminnet. Du kan också använda filtrering för att expandera den här vyn så att den innehåller andra resurser. Mer information om hur du arbetar med granskningsloggar finns i [Granskningsåtgärder med Resurshanteraren](../azure-resource-manager/management/view-activity-logs.md). Mer information om hur du övervakar Azure Cache for Redis-händelser finns i [Åtgärder och aviseringar](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Åtkomstkontroll (IAM)

Avsnittet **Åtkomstkontroll (IAM)** ger stöd för rollbaserad åtkomstkontroll (RBAC) i Azure-portalen. Den här konfigurationen hjälper organisationer att uppfylla sina åtkomsthanteringskrav enkelt och exakt. Mer information finns [i Rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Taggar

Avsnittet **Taggar** hjälper dig att ordna dina resurser. Mer information finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostisera och lösa problem

Klicka på **Diagnostisera och lösa problem** som ska förses med vanliga problem och strategier för att lösa dem.



## <a name="settings"></a>Inställningar
I avsnittet **Inställningar** kan du komma åt och konfigurera följande inställningar för cacheminnet.

* [Åtkomstnycklar](#access-keys)
* [Avancerade inställningar](#advanced-settings)
* [Azure Cache för Redis Advisor](#azure-cache-for-redis-advisor)
* [Skala](#scale)
* [Klusterstorlek](#cluster-size)
* [Data persistens](#redis-data-persistence)
* [Schemauppdateringar](#schedule-updates)
* [Geo-replikering](#geo-replication)
* [Virtual Network](#virtual-network)
* [Brandvägg](#firewall)
* [Egenskaper](#properties)
* [Lås](#locks)
* [Automationsskript](#automation-script)



### <a name="access-keys"></a>Åtkomstnycklar
Klicka på **Snabbtangenter** om du vill visa eller återskapa åtkomstnycklarna för cacheminnet. Dessa nycklar används av klienterna som ansluter till cachen.

![Azure Cache för Redis Access-nycklar](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Avancerade inställningar
Följande inställningar är konfigurerade på bladet **Avancerade inställningar.**

* [Åtkomstportar](#access-ports)
* [Minnesprinciper](#memory-policies)
* [Keyspace-meddelanden (avancerade inställningar)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Åtkomstportar
Som standard är icke-TLS/SSL-åtkomst inaktiverad för nya cacheminnen. Om du vill aktivera porten som inte är TLS klickar du på **Nej** för **tillåt åtkomst endast via SSL** på bladet Avancerade **inställningar** och klickar sedan på **Spara**.

> [!NOTE]
> TLS-åtkomst till Azure Cache för Redis stöder TLS 1.0, 1.1 och 1.2 för närvarande, men versionerna 1.0 och 1.1 dras snart tillbaka.  Läs vår [Ta bort TLS 1.0 och 1.1 sida](cache-remove-tls-10-11.md) för mer information.

![Azure Cache för Redis Access-portar](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Minnesprinciper
**Maxmemory-principen**, **maxmemory-reserverade**och **maxfragmentationmemory-reserverade** inställningar på bladet **Avancerade inställningar** konfigurerar minnesprinciperna för cacheminnet.

![Azure Cache för Redis Maxmemory-princip](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory-principen** konfigurerar vräkningsprincipen för cacheminnet och låter dig välja mellan följande vräkningsprinciper:

* `volatile-lru`- Det här är standardprincipen för vräkning.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Mer information `maxmemory` om principer finns i [Vräkningsprinciper](https://redis.io/topics/lru-cache#eviction-policies).

Den **maxmemory-reserverade** inställningen konfigurerar mängden minne, i MB, som är reserverad för icke-cacheåtgärder, till exempel replikering under redundans. Om du anger det här värdet kan du ha en mer konsekvent Redis-serverupplevelse när belastningen varierar. Det här värdet bör anges högre för arbetsbelastningar som skrivs tungt. När minnet är reserverat för sådana åtgärder är det inte tillgängligt för lagring av cachelagrade data.

Den **maxfragmentationmemory-reserverade** inställningen konfigurerar mängden minne i MB som är reserverad för minnesfragmentering. Om du anger det här värdet kan du ha en mer konsekvent Redis-serverupplevelse när cacheminnet är fullt eller nära fullt och fragmenteringsförhållandet är högt. När minnet är reserverat för sådana åtgärder är det inte tillgängligt för lagring av cachelagrade data.

En sak att tänka på när du väljer ett nytt minnesreservationsvärde **(maxmemory-reserverade** eller **maxfragmentationmemory-reserverade)** är hur den här ändringen kan påverka en cache som redan körs med stora mängder data i den. Om du till exempel har en 53 GB-cache med 49 GB data och sedan ändrar reservationsvärdet till 8 GB, kommer den här ändringen att släppa det högsta tillgängliga minnet för systemet ner till 45 GB. Om antingen `used_memory` dina `used_memory_rss` nuvarande eller dina värden är högre än den nya gränsen på 45 `used_memory` `used_memory_rss` GB, måste systemet vräka data tills båda och är under 45 GB. Vräkning kan öka serverbelastningen och minnesfragmenteringen. Mer information om cachemått `used_memory` som `used_memory_rss`och finns i [Tillgängliga mått och rapporteringsintervall](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> De **maxmemory-reserverade** och **maxfragmentationmemory-reserverade** inställningarna är endast tillgängliga för Standard- och Premium-cacheminnen.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace-meddelanden (avancerade inställningar)
Redis keyspace-meddelanden konfigureras på bladet **Avancerade inställningar.** Keyspace-meddelanden gör det möjligt för klienter att ta emot meddelanden när vissa händelser inträffar.

![Avancerade inställningar för Azure Cache för Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Keyspace-meddelanden och inställningen **notify-keyspace-events** är endast tillgängliga för Standard- och Premium-cacheminnen.
>
>

Mer information finns i [Redis Keyspace Notifications](https://redis.io/topics/notifications). Exempelkod finns i [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) filen i exemplet [Hello world.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache för Redis Advisor
**Bladet Azure Cache for Redis Advisor** visar rekommendationer för cacheminnet. Under normala åtgärder visas inga rekommendationer.

![Rekommendationer](./media/cache-configure/redis-cache-no-recommendations.png)

Om några villkor inträffar under cacheminnets åtgärder, till exempel hög minnesanvändning, nätverksbandbredd eller serverbelastning, visas en avisering på **Azure Cache for Redis-bladet.**

![Rekommendationer](./media/cache-configure/redis-cache-recommendations-alert.png)

Ytterligare information finns på **bladet Rekommendationer.**

![Rekommendationer](./media/cache-configure/redis-cache-recommendations.png)

Du kan övervaka dessa mått i avsnitten [Övervakningsdiagram](cache-how-to-monitor.md#monitoring-charts) och [användningsdiagram](cache-how-to-monitor.md#usage-charts) i **Azure Cache for Redis-bladet.**

Varje prisnivå har olika gränser för klientanslutningar, minne och bandbredd. Om cachen närmar sig maximal kapacitet för dessa mått under en längre tidsperiod skapas en rekommendation. Mer information om de mått och begränsningar som granskas av verktyget **Rekommendationer** finns i följande tabell:

| Azure Cache för Redis-mått | Mer information |
| --- | --- |
| Användning av nätverksbandbredd |[Cacheprestanda – tillgänglig bandbredd](cache-faq.md#cache-performance) |
| Anslutna klienter |[Standardkonfiguration för Redis-server - maxclients](#maxclients) |
| Serverbelastning |[Användningsdiagram - Redis Server Load](cache-how-to-monitor.md#usage-charts) |
| Minnesanvändning |[Cacheprestanda - storlek](cache-faq.md#cache-performance) |

Om du vill uppgradera cacheminnet klickar du på **Uppgradera nu** om du vill ändra prisnivån och [skala](#scale) cacheminnet. Mer information om hur du väljer en prisnivå finns i [Vad Azure Cache för Redis-erbjudande och storlek ska jag använda?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Skala
Klicka på **Skala** om du vill visa eller ändra prisnivån för cacheminnet. Mer information om skalning finns i [Skala Azure Cache för Redis](cache-how-to-scale.md).

![Azure Cache för Redis-prisnivå](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis-klusterstorlek
Klicka på **Klusterstorlek** om du vill ändra klusterstorleken för en premiumcache som körs med klusteraktiverad.

![Klusterstorlek](./media/cache-configure/redis-cache-redis-cluster-size.png)

Om du vill ändra klusterstorleken använder du skjutreglaget eller skriver ett tal mellan 1 och 10 i textrutan **Shard count** och klickar på **OK** för att spara.

> [!IMPORTANT]
> Redis-klustring är endast tillgängligt för Premium-cacheminnen. Mer information finns i [Så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Redis-datapersistens
Klicka på **Databeständighet** om du vill aktivera, inaktivera eller konfigurera databeständighet för premiumcachen. Azure Cache för Redis erbjuder Redis persistens med hjälp av antingen [RDB persistens](cache-how-to-premium-persistence.md#configure-rdb-persistence) eller [AOF persistens](cache-how-to-premium-persistence.md#configure-aof-persistence).

Mer information finns i [Så här konfigurerar du persistens för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Redis-data persistens är endast tillgängligt för Premium-cacheminnen.
>
>

### <a name="schedule-updates"></a>Schemauppdateringar
Med bladet **Schemauppdateringar** kan du ange ett underhållsfönster för Redis-serveruppdateringar för cacheminnet.

> [!IMPORTANT]
> Underhållsfönstret gäller endast Redis-serveruppdateringar och inte för några Azure-uppdateringar eller uppdateringar av operativsystemet för de virtuella datorer som är värdar för cachen.
>
>

![Schemauppdateringar](./media/cache-configure/redis-schedule-updates.png)

Om du vill ange ett underhållsfönster kontrollerar du önskade dagar och anger starttimmen för underhållsfönstret för varje dag och klickar på **OK**. Underhållsfönstret är i UTC.

> [!IMPORTANT]
> **Funktionen Schemauppdateringar** är endast tillgänglig för cacheminnen på Premium-nivån. Mer information och instruktioner finns i [Azure Cache för Redis-administration - Schemalägg uppdateringar](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Geo-replikering

**Geo-replikeringsbladet** tillhandahåller en mekanism för att länka två Azure-cache för Redis-premium-nivå för Redis-instanser. En cache betecknas som den primära länkade cachen och den andra som den sekundära länkade cachen. Den sekundära länkade cachen blir skrivskyddad och data som skrivs till den primära cachen replikeras till den sekundära länkade cachen. Den här funktionen kan användas för att replikera en cache över Azure-regioner.

> [!IMPORTANT]
> **Geo-replikering** är endast tillgängligt för Cacheminnen på Premium-nivå. Mer information och instruktioner finns i [Konfigurera Geo-replikering för Azure Cache för Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
Med avsnittet **Virtuellt nätverk** kan du konfigurera inställningarna för det virtuella nätverket för cacheminnet. Information om hur du skapar en premiumcache med VNET-stöd och uppdaterar dess inställningar finns i [Konfigurera stöd för virtuellt nätverk för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Inställningar för virtuella nätverk är endast tillgängliga för premiumcachen som har konfigurerats med VNET-stöd när cacheminnet skapades.
>
>

### <a name="firewall"></a>Brandvägg

Konfiguration av brandväggsregler är tillgänglig för alla Azure-cache för Redis-nivåer.

Klicka på **Brandvägg** om du vill visa och konfigurera brandväggsregler för cacheminne.

![Brandvägg](./media/cache-configure/redis-firewall-rules.png)

Du kan ange brandväggsregler med ett start- och slut-IP-adressintervall. När brandväggsregler har konfigurerats kan endast klientanslutningar från de angivna IP-adressintervallen ansluta till cachen. När en brandväggsregel sparas är det en kort fördröjning innan regeln gäller. Den här fördröjningen är vanligtvis mindre än en minut.

> [!IMPORTANT]
> Anslutningar från Azure Cache for Redis-övervakningssystem är alltid tillåtna, även om brandväggsregler är konfigurerade.
>
>

### <a name="properties"></a>Egenskaper
Klicka på **Egenskaper** om du vill visa information om cacheminnet, inklusive cacheslutpunkten och portarna.

![Azure Cache för Redis-egenskaper](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Lås
I avsnittet **Lås** kan du låsa en prenumeration, en resursgrupp eller resurs för att förhindra att andra användare i organisationen av misstag tar bort eller ändrar kritiska resurser. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automationsskript

Klicka på **Automation-skript** för att skapa och exportera en mall med dina distribuerade resurser för framtida distributioner. Mer information om hur du arbetar med mallar finns i [Distribuera resurser med Azure Resource Manager-mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Inställningar för administration
Med inställningarna i avsnittet **Administration** kan du utföra följande administrativa uppgifter för cacheminnet.

![Administration](./media/cache-configure/redis-cache-administration.png)

* [Importera data](#importexport)
* [Exportera data](#importexport)
* [Starta om](#reboot)


### <a name="importexport"></a>Import/Export
Import/export är en Azure Cache for Redis-datahanteringsåtgärd, som gör att du kan importera och exportera data i cacheminnet genom att importera och exportera en ögonblicksbild av Azure Cache for Redis Database (RDB) från en premiumcache till en sidblob i ett Azure Storage-konto. Med import/export kan du migrera mellan olika Azure-cache för Redis-instanser eller fylla i cacheminnet med data före användning.

Import kan användas för att få Redis-kompatibla RDB-filer från alla Redis-servrar som körs i alla moln eller miljöer, inklusive Redis som körs på Linux, Windows eller någon molnleverantör som Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa en cache med förifyllda data. Under importen läses RDB-filerna från Azure-lagringen in i minnet i Azure och infogar sedan nycklarna i cacheminnet.

Med export kan du exportera data som lagras i Azure Cache for Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache for Redis-instans till en annan eller till en annan Redis-server. Under exportprocessen skapas en temporär fil på den virtuella datorn som är värd för Azure Cache for Redis-serverinstansen och filen överförs till det angivna lagringskontot. När exportåtgärden slutförs med antingen status som lyckad eller misslyckad tas den temporära filen bort.

> [!IMPORTANT]
> Import/export är endast tillgängligt för cacheminnen på Premium-nivån. Mer information och instruktioner finns [i Importera och exportera data i Azure Cache för Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Starta om
**Med bladet Starta om** kan du starta om noderna i cacheminnet. Med den här omstartsfunktionen kan du testa programmet för återhämtning om det finns ett fel på en cachenod.

![Starta om](./media/cache-configure/redis-cache-reboot.png)

Om du har en premiumcache med klusteraktiverad kan du välja vilka shards i cacheminnet som ska startas om.

![Starta om](./media/cache-configure/redis-cache-reboot-cluster.png)

Om du vill starta om en eller flera noder i cacheminnet markerar du önskade noder och klickar på **Starta om**. Om du har en premiumcache med klusteraktiverad väljer du de shard(s) som ska startas om och klickar sedan på **Starta om**. Efter några minuter startar den valda noden/noderna om och är online igen några minuter senare.

> [!IMPORTANT]
> Omstart är nu tillgänglig för alla prisnivåer. Mer information och instruktioner finns i [Azure Cache for Redis administration - Reboot](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Övervakning

Med avsnittet **Övervakning** kan du konfigurera diagnostik och övervakning för din Azure-cache för Redis.
Mer information om Azure Cache for Redis övervakning och diagnostik finns i [Så här övervakar du Azure Cache för Redis](cache-how-to-monitor.md).

![Diagnostik](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-mått](#redis-metrics)
* [Varningsregler](#alert-rules)
* [Diagnostik](#diagnostics)

### <a name="redis-metrics"></a>Redis-mått
Klicka på **Redis-mått** om du vill [visa mått](cache-how-to-monitor.md#view-cache-metrics) för cacheminnet.

### <a name="alert-rules"></a>Varningsregler

Klicka på **Varningsregler** för att konfigurera aviseringar baserat på Azure Cache for Redis-mått. Mer information finns i [Aviseringar](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostik

Som standard lagras cachemått i Azure Monitor i [30 dagar](../azure-monitor/platform/data-platform-metrics.md) och tas sedan bort. Om du vill spara cachemåtten längre än 30 dagar klickar du på Diagnostik för att [konfigurera lagringskontot](cache-how-to-monitor.md#export-cache-metrics) som används för att lagra cachediagnostik. **Diagnostics**

>[!NOTE]
>Förutom att arkivera cachemåtten till lagring kan du också [strömma dem till en händelsenav eller skicka dem till Azure Monitor-loggar](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Stöd & felsökningsinställningar
Inställningarna i avsnittet **Support + felsökning** ger dig alternativ för att lösa problem med cacheminnet.

![Support och felsökning](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Resurshälsa](#resource-health)
* [Ny supportbegäran](#new-support-request)

### <a name="resource-health"></a>Resurshälsa
**Resurshälsan** bevakar din resurs och talar om för dig om den körs som förväntat. Mer information om hälsotjänsten för Azure Resource finns i [hälsoöversikten för Azure Resource](../resource-health/resource-health-overview.md).

> [!NOTE]
> Resurshälsa kan för närvarande inte rapportera om hälsotillståndet för Azure Cache för Redis-instanser som finns i ett virtuellt nätverk. Mer information finns i [Fungerar alla cachefunktioner när du är värd för en cache i ett VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Ny supportbegäran
Klicka på **Ny supportbegäran** om du vill öppna en supportbegäran för cacheminnet.





## <a name="default-redis-server-configuration"></a>Standardkonfiguration för Redis-server
Nya Azure-cache för Redis-instanser är konfigurerade med följande standardkonfigurationsvärden för Redis:

> [!NOTE]
> Det går inte att ändra `StackExchange.Redis.IServer.ConfigSet` inställningarna i det här avsnittet med hjälp av metoden. Om den här metoden anropas med något av kommandona i det här avsnittet genereras ett undantag som liknar följande exempel:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Alla värden som kan konfigureras, till exempel **max-minnesprincip,** kan konfigureras via Azure-portalen eller kommandoradshanteringsverktyg som Azure CLI eller PowerShell.
>
>

| Inställning | Standardvärde | Beskrivning |
| --- | --- | --- |
| `databases` |16 |Standardantalet databaser är 16 men du kan konfigurera ett annat nummer baserat på prisnivån. <sup>1</sup> Standarddatabasen är DB 0, du kan välja en `connection.GetDatabase(dbid)` annan `dbid` per anslutning `0` `databases - 1`med var är ett tal mellan och . |
| `maxclients` |Beror på prisnivån<sup>2</sup> |Det här värdet är det maximala antalet anslutna klienter som tillåts samtidigt. När gränsen har nåtts stänger Redis alla nya anslutningar och returnerar ett fel som har nåtts maximalt antal klienter. |
| `maxmemory-policy` |`volatile-lru` |Maxmemory-principen är inställningen för hur Redis `maxmemory` väljer vad som ska tas bort när (storleken på cachen som du valde när du skapade cacheminnet) nås. Med Azure Cache för Redis `volatile-lru`är standardinställningen , som tar bort nycklarna med en förfallodatumuppsättning med hjälp av en LRU-algoritm. Den här inställningen kan konfigureras i Azure-portalen. Mer information finns i [Minnesprinciper](#memory-policies). |
| `maxmemory-samples` |3 |För att spara minne är LRU- och minimala TTL-algoritmer ungefärliga algoritmer i stället för exakta algoritmer. Som standard Redis kontrollerar tre nycklar och väljer den som användes mindre nyligen. |
| `lua-time-limit` |5 000 |Max körningstid för ett Lua-skript i millisekunder. Om den maximala körningstiden har uppnåtts loggar Redis att ett skript fortfarande körs efter den maximala tillåtna tiden och börjar svara på frågor med ett fel. |
| `lua-event-limit` |500 |Max storlek på skripthändelsekön. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032MB 8MB 60 |Klientutdatabuffertgränserna kan användas för att tvinga frånkoppling av klienter som inte läser data från servern tillräckligt snabbt av någon anledning (en vanlig orsak är att en Pub/Sub-klient inte kan använda meddelanden så snabbt som utgivaren kan producera dem). Mer information finns [https://redis.io/topics/clients](https://redis.io/topics/clients)i . |

<a name="databases"></a>
<sup>1.</sup> Gränsen för `databases` är olika för varje Azure Cache för Redis-prisnivå och kan ställas in när cache skapas. Om `databases` ingen inställning anges när cache skapas är standardvärdet 16.

* Grundläggande och standardcachar
  * C0 (250 MB) cache - upp till 16 databaser
  * C1 (1 GB) cache - upp till 16 databaser
  * C2 (2,5 GB) cache - upp till 16 databaser
  * C3 (6 GB) cache - upp till 16 databaser
  * C4 (13 GB) cache - upp till 32 databaser
  * C5 (26 GB) cache - upp till 48 databaser
  * C6 (53 GB) cache - upp till 64 databaser
* Premium-cacheminnen
  * P1 (6 GB - 60 GB) - upp till 16 databaser
  * P2 (13 GB - 130 GB) - upp till 32 databaser
  * P3 (26 GB - 260 GB) - upp till 48 databaser
  * P4 (53 GB - 530 GB) - upp till 64 databaser
  * Alla premiumcachar med Redis-kluster aktiverat - Redis-klustret `databases` stöder endast användning av databas 0 så gränsen för alla premiumcacheminnen med Redis-kluster aktiverat är effektivt 1 och [kommandot Select](https://redis.io/commands/select) är inte tillåtet. Mer information finns i [Måste jag göra några ändringar i klientprogrammet för att använda klustring?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Mer information om databaser finns i [Vad är Redis-databaser?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> Inställningen `databases` kan endast konfigureras när cacheminnet skapas och endast med PowerShell, CLI eller andra hanteringsklienter. Ett exempel på konfiguration `databases` under cache skapande med PowerShell, se [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` är olika för varje Azure Cache för Redis-prisnivå.

* Grundläggande och standardcachar
  * C0 (250 MB) cache - upp till 256 anslutningar
  * C1 (1 GB) cache - upp till 1 000 anslutningar
  * C2 (2,5 GB) cache - upp till 2 000 anslutningar
  * C3 (6 GB) cache - upp till 5 000 anslutningar
  * C4 (13 GB) cache - upp till 10 000 anslutningar
  * C5 (26 GB) cache - upp till 15 000 anslutningar
  * C6 (53 GB) cache - upp till 20 000 anslutningar
* Premium-cacheminnen
  * P1 (6 GB - 60 GB) - upp till 7 500 anslutningar
  * P2 (13 GB - 130 GB) - upp till 15 000 anslutningar
  * P3 (26 GB - 260 GB) - upp till 30 000 anslutningar
  * P4 (53 GB - 530 GB) - upp till 40 000 anslutningar

> [!NOTE]
> Varje storlek på cachen tillåter *upp till* ett visst antal anslutningar, men varje anslutning till Redis har omkostnader som är associerade med den. Ett exempel på sådana omkostnader skulle vara CPU och minnesanvändning som ett resultat av TLS/SSL-kryptering. Den maximala anslutningsgränsen för en viss cachestorlek förutsätter en lätt inläst cache. Om belastningen från anslutningskostnaderna *plus* belastningen från klientåtgärder överskrider kapaciteten för systemet kan cachen uppstå kapacitetsproblem även om du inte har överskridit anslutningsgränsen för den aktuella cachestorleken.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis-kommandon stöds inte i Azure Cache för Redis
> [!IMPORTANT]
> Eftersom konfiguration och hantering av Azure Cache för Redis-instanser hanteras av Microsoft inaktiveras följande kommandon. Om du försöker anropa dem visas ett felmeddelande `"(error) ERR unknown command"`som liknar .
>
> * BGREWRITEAOF
> * BGSAVE (PÅ)
> * CONFIG
> * FELSÖK
> * Migrera
> * SPARA
> * Avstängning
> * SLAVEOF (SLAVEOF)
> * KLUSTER - Klusterskrivningskommandon är inaktiverade, men skrivskyddade klusterkommandon är tillåtna.
>
>

Mer information om Redis-kommandon [https://redis.io/commands](https://redis.io/commands)finns i .

## <a name="redis-console"></a>Redis-konsolen
Du kan på ett säkert sätt utfärda kommandon till dina Azure-cache för Redis-instanser med **Redis-konsolen**, som är tillgängligt i Azure-portalen för alla cachenivåer.

> [!IMPORTANT]
> - Redis-konsolen fungerar inte med [VNET](cache-how-to-premium-vnet.md). När cacheminnet är en del av ett virtuella nätverk kan endast klienter i det virtuella nätverket komma åt cachen. Eftersom Redis Console körs i din lokala webbläsare, som ligger utanför det virtuella nätverket, kan den inte ansluta till cacheminnet.
> - Alla Redis-kommandon stöds inte i Azure Cache för Redis. En lista över Redis-kommandon som är inaktiverade för Azure Cache för Redis finns i de tidigare [Redis-kommandona](#redis-commands-not-supported-in-azure-cache-for-redis) som inte stöds i avsnittet Azure Cache for Redis. Mer information om Redis-kommandon [https://redis.io/commands](https://redis.io/commands)finns i .
>
>

Om du vill komma åt Redis-konsolen klickar du på **Konsol** från **Azure Cache för Redis-bladet.**

![Redis-konsolen](./media/cache-configure/redis-console-menu.png)

Om du vill utfärda kommandon mot cacheinstansen skriver du önskat kommando i konsolen.

![Redis-konsolen](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Använda Redis-konsolen med en förstklassig klustercache

När du använder Redis-konsolen med en premium klustrade cache kan du utfärda kommandon till en enda fragment av cacheminnet. Om du vill utfärda ett kommando till en viss shard ansluter du först till önskad shard genom att klicka på den på fragmentväljaren.

![Redis-konsolen](./media/cache-configure/redis-console-premium-cluster.png)

Om du försöker komma åt en nyckel som lagras i en annan fragment än den anslutna shard, visas ett felmeddelande som liknar följande meddelande:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

I föregående exempel är fragment 1 den markerade `myKey` fragmentet, men finns i fragment `(shard 0)` 0, vilket indikeras av den del av felmeddelandet. I det här `myKey`exemplet väljer du shard 0 med hjälp av fragmentväljaren och utfärdar sedan önskat kommando.


## <a name="move-your-cache-to-a-new-subscription"></a>Flytta cacheminnet till en ny prenumeration
Du kan flytta cacheminnet till en ny prenumeration genom att klicka på **Flytta**.

![Flytta Azure-cache för Redis](./media/cache-configure/redis-cache-move.png)

Information om hur du flyttar resurser från en resursgrupp till en annan och från en prenumeration till en annan finns i [Flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du arbetar med Redis-kommandon finns i [Hur kör jag Redis-kommandon?](cache-faq.md#how-can-i-run-redis-commands)
