---
title: Introduktion till Azure Redis Cache Premium-nivån | Microsoft Docs
description: Lär dig hur du skapar och hanterar Redis-Persistence Redis-kluster och VNET-stöd för din Premium-nivån Azure Redis-Cache-instanser
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 38a43756678a3628040b1b995966eff6dd9fb363
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911211"
---
# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introduktion till Azure Redis-cache Premium-nivån
Azure Redis-Cache är en distribuerad, hanterade cache som hjälper dig att skapa mycket skalbart och dynamisk program genom att tillhandahålla mycket snabb åtkomst till dina data. 

Nya Premium-nivån är en klar nivå Enterprise, som innehåller alla funktioner som Standard-nivån och mycket mer, till exempel bättre prestanda, större arbetsbelastningar, katastrofåterställning, importera och exportera och förbättrad säkerhet. Läs mer om de ytterligare funktionerna i cache Premium-nivån.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Bättre prestanda jämfört med Standard- eller Basic-nivån
**Bättre prestanda över Standard eller grundläggande nivån.** Cacheminnena i Premium-nivån har distribuerats på maskinvara, vilket har snabbare processorer och ger bättre prestanda jämfört med till Basic eller Standard-nivån. Premium-nivån cacheminnen har högre genomflöde och lägre latens. 

**Dataflöde för samma storlek cachen är högre i Premium jämfört med standardnivån.** Till exempel genomflödet av 53 GB P4 (Premium) cache är 250K begäranden per sekund jämfört med 150 K för C6 (Standard).

Mer information om storlek, genomflöde och bandbredd med premium cacheminnen finns [Azure Redis-Cache vanliga frågor och svar](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis datapersistence
Premium-nivån kan du bevara Cachedata i ett Azure Storage-konto. Alla data lagras i en grundläggande/Standard cache endast i minnet. Om underliggande infrastruktur kan problem det vara potentiell dataförlust. Vi rekommenderar att du använder funktionen Redis data beständiga i Premium-nivån för att öka återhämtningsförmågan mot dataförlust. Azure Redis-Cache erbjuder RDB och AOF (kommer snart) alternativ i [Redis-persistence](http://redis.io/topics/persistence). 

Instruktioner om hur du konfigurerar persistence finns i [Konfigurera persistence för premium Azure Redis-cache](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Redis-kluster
Om du vill skapa cacheminnen större än 53 GB eller vill Fragmentera data över flera Redis-noder kan du använda Redis-kluster som är tillgängliga i Premium-nivån. Varje nod består av ett par för cache av primära/replik som hanteras av Azure för hög tillgänglighet. 

**Redis-kluster ger dig maximal skala och genomflöde.** Genomströmning ökar linjärt när du ökar antalet delar (noder) i klustret. T.ex. Om du skapar ett kluster P4 av 10 shards, så det tillgängliga genomflödet 250K * 10 = 2,5 miljoner förfrågningar per sekund. Finns det [Azure Redis-Cache vanliga frågor och svar](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) för mer information om storlek, genomflöde och bandbredd med premium cacheminnen.

Om du vill komma igång med kluster, se [konfigurera klustring för Premium Azure Redis-Cache](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Förbättrad säkerhet och isolering
Cacheminnet som skapats i Basic eller Standard-nivån är tillgängliga på internet. Åtkomst till cachen är begränsad baserat på snabbtangent. Med Premium-nivån du ytterligare se till att endast klienter inom ett angivet nätverk har åtkomst till cachen. Du kan distribuera Redis-Cache i en [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). Du kan använda alla VNet-funktioner, till exempel undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten till Redis.

Mer information finns i [Konfigurera Virtual Network-stöd för premium Azure Redis-cache](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Import/Export är en Azure Redis-Cache data management-åtgärd där du kan importera data till Azure Redis-Cache eller exportera data från Azure Redis-Cache genom att importera och exportera en ögonblicksbild för Redis-Cache databasen (RDB) från cache premium till en sidblob i ett Azure Storage-konto. På så sätt kan du migrera mellan olika instanser av Azure Redis-Cache eller fylla i cachen med data innan de används.

Import kan användas för att hämta Redis kompatibel RDB filer från en Redis-server som kör i molnet eller miljö, inklusive Redis som körs på Linux, Windows eller valfri provider som molntjänster, till exempel Amazon Web Services och andra. Importera data är ett enkelt sätt att skapa ett cacheminne med data i förväg. Under importen, Azure Redis-Cache läser in RDB-filer från Azure storage i minnet och infogar nycklarna i cacheminnet.

Exportera kan du exportera de data som lagras i Azure Redis-Cache för Redis-kompatibel RDB fil(er). Du kan använda den här funktionen för att flytta data från en Azure Redis-Cache-instans till en annan eller till en annan Redis-server. Skapa en tillfällig fil på den virtuella datorn som är värd för Azure Redis-Cache server-instansen under exporten, och överföra filen till det angivna lagringskontot. När exporten har slutförts med antingen en status för lyckad eller misslyckad, tas den temporära filen bort.

Mer information finns i [hur du importerar data till och exportera data från Azure Redis-Cache](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Starta om
Premium-nivån kan du starta om en eller flera noder i din cache på begäran. På så sätt kan du testa ditt program för återhämtning i händelse av ett fel. Du kan starta om följande noder.

* Huvudnod i ditt cacheminne
* Underordnade noder i ditt cacheminne
* Huvud- och underordnade noder i ditt cacheminne
* När du använder en premium-cache med kluster måste du starta om huvudservern, slavserver eller båda noderna för enskilda delar i cacheminnet

Mer information finns i [omstart](cache-administration.md#reboot) och [omstart vanliga frågor och svar](cache-administration.md#reboot-faq).

>[!NOTE]
>Starta om funktionen har nu aktiverats för alla nivåer för Azure Redis-Cache.
>
>

## <a name="schedule-updates"></a>Schemauppdateringar
Den schemalagda uppdateringar kan du utse en underhållsperiod för ditt cacheminne. När underhållsfönstret har angetts görs alla uppdateringar för Redis-servern under den här perioden. Om du vill ange en underhållsperiod, Välj önskade dagar och ange underhållet fönstret Starttimme för varje dag. Observera att fönstret Underhåll i UTC. 

Mer information finns i [schemalägga uppdateringar](cache-administration.md#schedule-updates) och [Schemalägg uppdateringar vanliga frågor och svar](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Endast Redis-server uppdateringar har gjorts under den schemalagda underhållsperioden. Underhållsfönstret gäller inte för Azure-uppdateringar eller uppdateringar av operativsystemet VM.
> 
> 

## <a name="geo-replication"></a>Geo-replikering

**GEO-replikering** tillhandahåller en mekanism för att länka två instanser i Premium-nivån Azure Redis-Cache. En cache betecknas som primär länkade cacheminnet och andra som sekundär länkade cache. Sekundär länkade cachen skrivskyddas och data som skrivs till den primära cachen replikeras till sekundär länkade cachen. Den här funktionen kan användas för att replikera en cache över Azure-regioner.

Mer information finns i [hur du konfigurerar Geo-replikering för Azure Redis-Cache](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Att skala till premium-nivån
Om du vill skala till premium-nivån, Välj en av premiumnivå i den **ändra prisnivån** bladet. Du kan även skala ditt cacheminne till premium-nivån med PowerShell och CLI. Stegvisa instruktioner finns [så skala Azure Redis-Cache](cache-how-to-scale.md) och [automatisera en åtgärd för skalning](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Nästa steg
Skapa ett cacheminne och utforska de nya funktionerna för premium-nivån.

* [Så här konfigurerar du persistence för Premium Azure Redis Cache](cache-how-to-premium-persistence.md)
* [Så här konfigurerar du Virtual Network-stöd för Premium Azure Redis Cache](cache-how-to-premium-vnet.md)
* [Så här konfigurerar du klustring för Premium Azure Redis Cache](cache-how-to-premium-clustering.md)
* [Hur du importera data till och exportera data från Azure Redis-Cache](cache-how-to-import-export-data.md)
* [Hur man administrerar Azure Redis-Cache](cache-administration.md)

