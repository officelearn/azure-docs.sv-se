---
title: Introduktion till Azure Cache för Redis Premium-nivå
description: Lär dig hur du skapar och hanterar Redis Persistens-, Redis-kluster- och VNET-stöd för Azure-cache för Redis-instanser på Premium-nivå
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74121664"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introduktion till Azure Cache för Redis Premium-nivå
Azure Cache för Redis är en distribuerad, hanterad cache som hjälper dig att skapa mycket skalbara och responsiva program genom att ge supersnabb åtkomst till dina data. 

Den nya Premium-nivån är en Enterprise-förberedd nivå, som innehåller alla standardnivåfunktioner med mera, till exempel bättre prestanda, större arbetsbelastningar, haveriberedskap, import/export och förbättrad säkerhet. Fortsätt läsa om du vill veta mer om de ytterligare funktionerna på Premium-cachenivån.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Bättre prestanda jämfört med standard- eller basic-nivå
**Bättre prestanda över standard- eller basic-nivå.** Cacheminnen på Premium-nivån distribueras på maskinvara som har snabbare processorer och ger bättre prestanda jämfört med basic- eller standardnivån. Premium-nivåcachar har högre dataflöde och lägre svarstider. 

**Dataflödet för cacheminnet av samma storlek är högre i Premium jämfört med standardnivå.** Till exempel är dataflödet för en 53 GB P4 (Premium) cache 250K-begäranden per sekund jämfört med 150K för C6 (Standard).

Mer information om storlek, dataflöde och bandbredd med premiumcachen finns i [vanliga frågor och svar om Azure Cache för Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis-datapersistens
Med Premium-nivån kan du spara cachedata i ett Azure Storage-konto. I en Basic/Standard-cache lagras alla data endast i minnet. Vid underliggande infrastrukturproblem kan det finnas potentiella dataförluster. Vi rekommenderar att du använder Redis-funktionen för beständighet i Premium-nivån för att öka återhämtningen mot dataförlust. Azure Cache för Redis erbjuder RDB och AOF (kommer snart) alternativ i [Redis persistens](https://redis.io/topics/persistence). 

Instruktioner om hur du konfigurerar persistens finns i [Så här konfigurerar du persistens för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Redis-kluster
Om du vill skapa cacheminnen som är större än 53 GB eller vill fragmentera data över flera Redis-noder kan du använda Redis-kluster som är tillgängligt på Premium-nivån. Varje nod består av ett primärt/replikcachepar som hanteras av Azure för hög tillgänglighet. 

**Redis-klustring ger dig maximal skala och dataflöde.** Dataflödet ökar linjärt när du ökar antalet shards (noder) i klustret. T.ex Om du skapar ett P4-kluster med 10 shards är det tillgängliga dataflödet 250K *10 = 2,5 miljoner begäranden per sekund. Se [vanliga frågor och svar om Azure Cache för Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) för mer information om storlek, dataflöde och bandbredd med premiumcachar.

Information om hur du kommer igång med klustring finns i [Konfigurera klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Förbättrad säkerhet och isolering
Cacheminnen som skapas på basic- eller standardnivån är tillgängliga på det offentliga internet. Åtkomsten till cachen är begränsad baserat på åtkomstnyckeln. Med Premium-nivån kan du ytterligare se till att endast klienter i ett angivet nätverk kan komma åt cachen. Du kan distribuera Azure-cache för Redis i ett [virtuellt Azure-nätverk (VNet).](https://azure.microsoft.com/services/virtual-network/) Du kan använda alla VNet-funktioner, till exempel undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten till Redis.

Mer information finns i [Så här konfigurerar du stöd för virtuellt nätverk för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Import/export är en Azure Cache for Redis-datahanteringsåtgärd som gör att du kan importera data till Azure Cache for Redis eller exportera data från Azure Cache for Redis genom att importera och exportera en ögonblicksbild av Azure Cache for Redis Database (RDB) från en premiumcache till en sidblob i ett Azure Storage-konto. På så sätt kan du migrera mellan olika Azure-cache för Redis-instanser eller fylla i cacheminnet med data före användning.

Import kan användas för att föra Redis-kompatibla RDB-filer från alla Redis-servrar som körs i alla moln eller miljöer, inklusive Redis som körs på Linux, Windows eller någon molnleverantör som Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa en cache med förifyllda data. Under importen läses RDB-filerna från Azure-lagringen in i minnet och infogar sedan nycklarna i cacheminnet.

Med export kan du exportera data som lagras i Azure Cache for Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache for Redis-instans till en annan eller till en annan Redis-server. Under exportprocessen skapas en temporär fil på den virtuella datorn som är värd för Azure Cache for Redis-serverinstansen och filen överförs till det angivna lagringskontot. När exportåtgärden slutförs med antingen status som lyckad eller misslyckad tas den temporära filen bort.

Mer information finns i [Så här importerar du data till och exporterar data från Azure Cache för Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Starta om
Med premiumnivån kan du starta om en eller flera noder i cacheminnet på begäran. På så sätt kan du testa ditt program för återhämtning i händelse av ett fel. Du kan starta om följande noder.

* Huvudnoden för cacheminnet
* Sekundär nod i cacheminnet
* Både primära och sekundära noder i cacheminnet
* När du använder en premiumcache med klustring kan du starta om de primära, sekundära eller båda noderna för enskilda shards i cacheminnet

Mer information finns i [Vanliga frågor och](cache-administration.md#reboot) svar [om](cache-administration.md#reboot-faq)omstart .

>[!NOTE]
>Omstartsfunktioner är nu aktiverade för alla Azure Cache för Redis-nivåer.
>
>

## <a name="schedule-updates"></a>Schemauppdateringar
Med funktionen schemalagda uppdateringar kan du ange ett underhållsfönster för cacheminnet. När underhållsfönstret har angetts görs alla Redis-serveruppdateringar under det här fönstret. Om du vill ange ett underhållsfönster väljer du önskade dagar och anger starttimmen för underhållsfönstret för varje dag. Observera att underhållsfönstret är i UTC. 

Mer information finns i Vanliga frågor och svar [om schemalägg uppdateringar](cache-administration.md#schedule-updates) [och Schemalägga uppdateringar](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Endast Redis-serveruppdateringar görs under det schemalagda underhållsfönstret. Underhållsfönstret gäller inte för Azure-uppdateringar eller uppdateringar av operativsystemet VM.
> 
> 

## <a name="geo-replication"></a>Geo-replikering

**Geo-replikering** är en mekanism för att länka två Azure-cache på Premium-nivå för Redis-instanser. En cache betecknas som den primära länkade cachen och den andra som den sekundära länkade cachen. Den sekundära länkade cachen blir skrivskyddad och data som skrivs till den primära cachen replikeras till den sekundära länkade cachen. Den här funktionen kan användas för att replikera en cache över Azure-regioner.

Mer information finns i [Så här konfigurerar du Geo-replikering för Azure Cache för Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Så här skalar du till premiumnivån
Om du vill skala till premiumnivån väljer du helt enkelt en av premiumnivåerna i bladet **Ändra prisnivå.** Du kan också skala cachen till premiumnivån med PowerShell och CLI. Stegvisa instruktioner finns i [Skala Azure Cache för Redis](cache-how-to-scale.md) och [så här automatiserar du en skalningsåtgärd](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Nästa steg
Skapa en cache och utforska de nya premiumnivåfunktionerna.

* [Konfigurera persistens för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md)
* [Konfigurera stöd för virtuellt nätverk för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md)
* [Konfigurera klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md)
* [Importera data till och exportera data från Azure Cache för Redis](cache-how-to-import-export-data.md)
* [Så här administrerar du Azure Cache for Redis](cache-administration.md)

