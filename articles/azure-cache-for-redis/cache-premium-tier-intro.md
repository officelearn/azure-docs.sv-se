---
title: Introduktion till Azure Cache för Premium-nivån Redis | Microsoft Docs
description: Lär dig att skapa och hantera Redis-persistens, Redis-klustring och VNET-stöd för din Premium-nivån Azure Cache för Redis-instanser
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 0978e906467fd5b16f25bd2e053980dda4510127
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53020097"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introduktion till Azure Cache Redis Premium-nivån
Azure Cache för Redis är ett distribuerat och hanterat cache som hjälper dig att bygga skalbara och tillgängliga program med Supersnabb åtkomst till dina data. 

Den nya Premium-nivån är en företagsklar nivå, som omfattar alla funktioner på Standard-nivån och mer, till exempel bättre prestanda, större arbetsbelastningar, haveriberedskap, import/export- och förbättrad säkerhet. Läs vidare om du vill veta mer om de ytterligare funktionerna i Premium-cache-nivå.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Bättre prestanda jämfört med Standard- eller Basic-nivån
**Bättre prestanda över Standard eller grundläggande nivå.** Cacheminnen på Premium-nivån har distribuerats på maskinvara som har snabbare processorer och ger bättre prestanda jämfört med Basic eller Standard-nivån. Premium-nivån cacheminnen har högre dataflöde och kortare svarstider. 

**Dataflöde för samma storlek cachen är högre i Premium jämfört med Standard-nivån.** Till exempel dataflödet för en 53 GB P4 (Premium) cache är 250K begäranden per sekund jämfört med 150 K för C6 (Standard).

Mer information om storlek, dataflöde och bandbredd med premium-cache finns i [Azure Cache för Redis vanliga frågor och svar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis-datapersistens
Premium-nivån kan du spara Cachedata i ett Azure Storage-konto. Alla data lagras endast i minnet i en Basic-och Standard-cache. När det gäller underliggande infrastruktur kan problem det vara potentiell dataförlust. Vi rekommenderar att du använder Redis-datapersistensfunktionen på Premium-nivån för att öka skyddet mot dataförlust. Azure Cache för Redis erbjuder RDB och AOF (kommer snart) alternativ i [Redis persistence](http://redis.io/topics/persistence). 

Anvisningar om hur du konfigurerar persistence finns i [så här konfigurerar du persistence för Premium Azure Cache för Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Redis-kluster
Om du vill skapa cacheminnen större än 53 GB eller Fragmentera data över flera Redis-noder kan du använda Redis-klustring som är tillgängligt på Premium-nivån. Varje nod består av ett par för primär/replik-cache som hanteras av Azure för hög tillgänglighet. 

**Redis-klustring får du maximal skala och genomströmning.** Dataflödet ökar linjärt när du ökar antalet shards (noder) i klustret. T.ex. Om du skapar ett P4-kluster med 10 shards, så det tillgängliga genomflödet är 250K * 10 = 2,5 miljoner förfrågningar per sekund. Finns det [Azure Cache för Redis vanliga frågor och svar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) för mer information om storlek, dataflöde och bandbredd med premium-cacheminnen.

Kom igång med klustring, se [så här konfigurerar du klustring för Premium Azure Cache för Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Förbättrad säkerhet och isolering
Cacheminnen som skapats i Basic eller Standard-nivån är tillgängliga på internet. Åtkomst till cachen begränsas baserat på åtkomstnyckeln. Med Premium-nivån du ytterligare se till att endast klienter inom ett angivet nätverk har åtkomst till cachen. Du kan distribuera Azure Cache för Redis i en [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). Du kan använda alla VNet-funktioner, till exempel undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten till Redis.

Mer information finns i [så här konfigurerar du Virtual Network-stöd för Premium Azure Cache för Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Import/Export är en Azure-Cache för Redis data management-åtgärd där du kan importera data till Azure Cache för Redis eller exportera data från Azure Cache för Redis genom att importera och exportera en Azure-Cache för Redis-databasen (RDB) ögonblicksbild från en premium-cache för en sidblob i ett Azure Storage-konto. På så sätt kan du migrera mellan olika Azure-Cache för Redis-instanser eller fylla i cachen med data före användning.

Import kan användas för att hämta Redis kompatibla RDB-fil(er) från en Redis-server som kör i valfritt moln eller en miljö, inklusive Redis som körs på Linux, Windows eller någon annan molnleverantör, till exempel Amazon Web Services och andra. Importera data är ett enkelt sätt att skapa ett cacheminne med fyllda. Under importen, Azure Cache för Redis läser in RDB-filer från Azure storage i minnet och sedan infogar nycklarna i cachen.

Exportera kan du exportera de data som lagras i Azure Cache för Redis Redis kompatibla RDB-fil(er). Du kan använda den här funktionen för att flytta data från en Azure Cache för Redis-instans till en annan eller till en annan Redis-server. Under exporten, skapas en temporär fil på den virtuella datorn som är värd för Azure Cache för Redis server-instansen och filen har överförts till det angivna lagringskontot. När exporten är klar med antingen en status för en lyckad eller misslyckad tas den tillfälliga filen bort.

Mer information finns i [importera data till och exportera data från Azure Cache för Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Starta om
Premium-nivån kan du starta om en eller flera noder i din cache på begäran. På så sätt kan du testa ditt program för återhämtning i händelse av ett fel. Du kan starta om följande noder.

* Huvudnoden för din cachelagring
* Underordnad nod för din cachelagring
* Huvud- och underordnade noder för din cachelagring
* När du använder en premium-cache med klustring, du kan starta om huvudservern, underordnad server eller båda noderna för enskilda fragment i cacheminnet

Mer information finns i [omstart](cache-administration.md#reboot) och [starta om vanliga frågor och svar](cache-administration.md#reboot-faq).

>[!NOTE]
>Starta om funktionen är nu aktiverad för alla Azure-Cache för Redis-nivåerna.
>
>

## <a name="schedule-updates"></a>Schemauppdateringar
Funktionen för schemalagda uppdateringar kan du ange en underhållsperiod för cacheminnet. När underhållsfönstret har angetts görs alla uppdateringar för Redis-servern under det här fönstret. Om du vill ange en underhållsperiod, välja önskade dagar och ange underhållet starta fönstret timme för varje dag. Observera att underhållsfönstertiden är i UTC. 

Mer information finns i [Schemalägg uppdateringar](cache-administration.md#schedule-updates) och [Schemalägg uppdateringar vanliga frågor och svar](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Endast Redis-server som har uppdaterats under det schemalagda underhållsfönstret. Underhållsperioden gäller inte för Azure-uppdateringar eller uppdateringar av VM-operativsystem.
> 
> 

## <a name="geo-replication"></a>Geo-replikering

**GEO-replikering** är en mekanism för att länka två Premium-nivån Azure Cache för Redis-instanser. En cache anges den primära länkade cachen och den andra som sekundär länkat cacheminne. Sekundär länkade cacheminnet blir skrivskyddade och data som skrivs till den primära cachen replikeras till det sekundära länkat cacheminnet. Den här funktionen kan användas för att replikera en cache i Azure-regioner.

Mer information finns i [konfigurera Geo-replikering för Azure Cache för Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Att skala till premium-nivån
Skalar till premium-nivån kan du bara välja en av premium-nivåerna i den **ändra prisnivån** bladet. Du kan även skala ditt cacheminne till premium-nivån med PowerShell och CLI. Stegvisa instruktioner finns i [så skala Azure Cache för Redis](cache-how-to-scale.md) och [automatisera en åtgärd för skalning](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Nästa steg
Skapa en cache och utforska de nya funktionerna på premiumnivå.

* [Så här konfigurerar du persistence för Premium Azure Cache för Redis](cache-how-to-premium-persistence.md)
* [Så här konfigurerar du Virtual Network-stöd för Premium Azure Cache för Redis](cache-how-to-premium-vnet.md)
* [Så här konfigurerar du klustring för Premium Azure Cache för Redis](cache-how-to-premium-clustering.md)
* [Hur du importera data till och exportera data från Azure Cache för Redis](cache-how-to-import-export-data.md)
* [Hur du administrerar Azure Cache för Redis](cache-administration.md)

