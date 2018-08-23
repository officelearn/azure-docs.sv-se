---
title: Azure SQL Database DTU-baserad resurs begränsar elastiska pooler | Microsoft Docs
description: Den här sidan beskriver några vanliga DTU-baserade resursbegränsningar för elastiska pooler i Azure SQL Database.
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 0920489daf99445a9d715d0858cf68ae45e6a178
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055607"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-based-purchasing-model"></a>Resurser-gränser för elastiska pooler med den DTU-baserade inköpsmodellen 

Den här artikeln innehåller detaljerade resursgränser för Azure SQL Database elastiska pooler och databaser i en pool med hjälp av den DTU-baserade inköpsmodellen. 

DTU-baserade inköpschef modellen resursgränser för enskilda databaser, se [DTU-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-elastic-pools.md). VCore-baserade resursbegränsningar finns [vCore-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastisk pool: lagringsstorlekar och prestandanivåer

Följande tabeller visar de tillgängliga resurserna på varje servicenivå för tjänstnivå och prestandanivå för SQL Database elastiska pooler. Du kan ange tjänstnivån, prestandanivån och storage mycket med den [Azure-portalen](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), eller [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Resursbegränsningar enskilda databaser i elastiska pooler är vanligtvis samma som för enskilda databaser utanför poolerna baserat på dtu: er och tjänstnivån. Till exempel max samtidiga arbetare för en S2-databas är 120 arbetare. Max samtidiga arbetare för en databas i en Standard-pool är därför också 120 arbetare om max DTU per databas i poolen är 50 dtu: er (som motsvarar S2).

### <a name="basic-elastic-pool-limits"></a>Gränser för grundläggande elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Inkluderad lagring per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Alternativ för maximal lagring per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximal InMemory-OLTP-lagring per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas | Gäller inte |
| Maximalt antal databaser per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximalt antal samtidiga arbetare (begäranden) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Alternativ för minsta edtu: er per databas | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Alternativ för högsta antal edtu: er per databas | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Största lagringsutrymme per databas (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Standardgränser för elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Inkluderad lagring per pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Alternativ för maximal lagring per pool (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Maximal InMemory-OLTP-lagring per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Saknas | Gäller inte | 
| Maximalt antal databaser per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Alternativ för minsta edtu: er per databas | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Alternativ för högsta antal edtu: er per databas | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Största lagringsutrymme per databas (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standardgränser för elastisk pool (forts.) 

| eDTU:er per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Inkluderad lagring per pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Alternativ för maximal lagring per pool (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maximal InMemory-OLTP-lagring per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Gäller inte | 
| Maximalt antal databaser per pool | 500 | 500 | 500 | 500 | 500 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Alternativ för minsta edtu: er per databas | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Alternativ för högsta antal edtu: er per databas | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Alternativ för maximal lagring per databas (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Gränser för Premium elastisk pool

| eDTU:er per pool | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Inkluderad lagring per pool (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Alternativ för maximal lagring per pool (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maximal InMemory-OLTP-lagring per pool (GB) | 1 | 2 | 4 | 10 | 12 | 
| Maximalt antal databaser per pool | 50 | 100 | 100 | 100 | 100 | 
| Maximalt antal samtidiga arbetare per pool (begäranden) | 200 | 400 | 800 | 1600 | 2400 | 
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Minimalt antal eDTU:er per databas | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Maximalt antal eDTU:er per databas | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Största lagringsutrymme per databas (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Premiumgränser för elastisk pool (forts.) 

| eDTU:er per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Inkluderad lagring per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Alternativ för maximal lagring per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Maximal InMemory-OLTP-lagring per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximalt antal databaser per pool | 100 | 100 | 100 | 100 | 100 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Alternativ för minsta edtu: er per databas | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Alternativ för högsta antal edtu: er per databas | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Största lagringsutrymme per databas (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> Mer än 1 TB av lagringsutrymmet på Premium-nivån är för närvarande tillgängligt i alla regioner förutom följande: västra centrala USA, Kina, östra, centrala USDoDCentral, Tyskland, USDoDEast USA-förvaltad region sydväst, Tyskland, nordöstra, USGov Iowa, Kina, norra. Det maximala lagringsutrymmet på Premium-nivån i andra regioner är begränsat till 1 TB. Se [sidan 11-15 i Aktuella begränsningar](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

Om alla DTU:er för en elastisk pool används får varje databas i poolen lika många resurser för att bearbeta frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. En rättvis resursdelning i elastiska pooler tillämpas utöver den mängd resurser som varje databas är garanterad om det minsta antalet DTU:er per databas har angetts till ett annat värde än noll.

### <a name="database-properties-for-pooled-databases"></a>Databasegenskaperna för databaser i en pool

I följande tabell beskrivs egenskaperna för databaser i en pool.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Maximalt antal eDTU:er per databas |Det högsta antalet eDTU:er som en databas i poolen kan använda, om de är tillgängliga beroende på användningen av andra databaser i poolen. Det högsta antalet eDTU:er per databas utgör ingen resursgaranti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange ett högsta antal eDTU:er per databas som är tillräckligt högt för att hantera toppar i databasanvändningen. En viss grad av övertilldelning är att förvänta eftersom poolen normalt arbetar efter ett mönster med frekvent och lågfrekvent användning av databaser där inte alla databaser har aktivitetstoppar samtidigt. Anta exempelvis att toppanvändningen per databas är 20 eDTU:er och att endast 20 % av de 100 databaserna i poolen har hög belastning samtidigt. Om det högsta antalet eDTU:er per databas har angetts till 20 eDTU:er är det rimligt att övertilldela poolen med 5 gånger så mycket och ange eDTU:erna per pool till 400. |
| Minimalt antal eDTU:er per databas |Det minsta antalet eDTU:er som en databas i poolen är garanterad. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Det minsta antalet eDTU:er per databas kan anges till 0, vilket även är standardvärdet. Den här egenskapen anges till ett värde mellan 0 och den genomsnittliga eDTU-användningen per databas. Produkten av antalet databaser i poolen och det minsta antalet eDTU:er per databas får inte överskrida antalet eDTU:er per pool. Om en pool till exempel har 20 databaser och det minsta antalet eDTU:er per databas har angetts till 10 eDTU:er så måste antalet eDTU:er per pool vara minst 200 eDTU:er. |
| Maximalt lagringsutrymme per databas |Den maximala databasstorleken som anges av användaren för en databas i poolen. Pooldatabaser delar dock allokerade lagringsutrymme. Även om det totala lagringsutrymmet som max *per databas* anges vara större än det totala lagringsutrymmet som tillgängliga *utrymme i poolen*, totalt utrymme som faktiskt används av alla databaserna kommer inte att kunna överskrida den tillgängliga poolen gräns. Maximal databasstorlek avser den maximala storleken för datafilerna och omfattar inte utrymmet som används av loggfiler. |
|||
 


## <a name="next-steps"></a>Nästa steg

- Se [SQL Database vanliga frågor och svar](sql-database-faq.md) svar på vanliga frågor och svar.
- Se [översikt över Azure SQL Database-resursgränser](sql-database-resource-limits.md) information om begränsningar på de server och prenumeration.
- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om dtu: er och edtu: er finns i [dtu: er och edtu: er](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Information om storleksgränser för tempdb finns i https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
