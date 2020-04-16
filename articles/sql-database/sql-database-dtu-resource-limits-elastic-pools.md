---
title: DTU-resursen begränsar elastiska pooler
description: På den här sidan beskrivs några vanliga DTU-resursgränser för elastiska pooler i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 76085dc29d40944cf704dbc5efc578b3314f499a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419246"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Resursgränser för elastiska pooler med hjälp av DTU-inköpsmodellen

Den här artikeln innehåller detaljerade resursgränser för elastiska Azure SQL Database-pooler och poolade databaser med hjälp av DTU-inköpsmodellen.

Information om resursgränser för DTU-inköpsmodell för enskilda databaser finns i [DTU-resursgränser – enskilda databaser](sql-database-vcore-resource-limits-elastic-pools.md). För vCore-resursgränser, se [vCore-resursgränser - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-resursgränser - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastisk pool: Lagringsstorlekar och beräkningsstorlekar

För elastiska SQL-databaspooler visar följande tabeller de resurser som är tillgängliga på varje tjänstnivå och beräkningsstorlek. Du kan ange tjänstnivå, beräkningsstorlek och lagringsbelopp med [Azure-portalen,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)eller [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> För skalning av vägledning och överväganden finns i [Skala en elastisk pool](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Resursgränserna för enskilda databaser i elastiska pooler är i allmänhet desamma som för enskilda databaser utanför pooler baserat på DTI:er och tjänstnivån. Till exempel är max samtidiga arbetare för en S2-databas 120 arbetare. Så, max samtidiga arbetare för en databas i en standardpool är också 120 arbetare om max DTU per databas i poolen är 50 DTU (vilket motsvarar S2).

> [!NOTE]
> Lagrings-per poolresursgränsen i var och en av följande tabeller innehåller inte tempdb- och logglagring.

### <a name="basic-elastic-pool-limits"></a>Gränser för grundläggande elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Inkluderat lagringsutrymme per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max lagringsalternativ per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Högsta OLTP-lagring i minnet per pool (GB) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |
| Max antal DBs per pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Max samtidiga arbetare (begäranden) per pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Max samtidiga sessioner per pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Alternativ för min eDTUs per databas | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Max val av eDTUs per databas | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Största lagringsutrymme per databas (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

### <a name="standard-elastic-pool-limits"></a>Standardgränser för elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Inkluderat lagringsutrymme per pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Max lagringsalternativ per pool (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Högsta OLTP-lagring i minnet per pool (GB) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |
| Max antal DBs per pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Max samtidiga arbetare (begäranden) per pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Max samtidiga sessioner per pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Alternativ för min eDTUs per databas | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Max val av eDTUs per databas | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Största lagringsutrymme per databas (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

### <a name="standard-elastic-pool-limits-continued"></a>Standardgränser för elastisk pool (forts.)

| eDTU:er per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Inkluderat lagringsutrymme per pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Max lagringsalternativ per pool (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Högsta OLTP-lagring i minnet per pool (GB) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt |
| Max antal DBs per pool <sup>1</sup> | 500 | 500 | 500 | 500 | 500 |
| Max samtidiga arbetare (begäranden) per pool <sup>2</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Max samtidiga sessioner per pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Alternativ för min eDTUs per databas | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max val av eDTUs per databas | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max lagringsalternativ per databas (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

### <a name="premium-elastic-pool-limits"></a>Gränser för Premium elastisk pool

| eDTU:er per pool | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Inkluderat lagringsutrymme per pool (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Max lagringsalternativ per pool (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Högsta OLTP-lagring i minnet per pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Max antal DBs per pool <sup>1</sup> | 50 | 100 | 100 | 100 | 100 |
| Max samtidiga arbetare per pool (begäranden) <sup>2</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Max samtidiga sessioner per pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalt antal eDTU:er per databas | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Maximalt antal eDTU:er per databas | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Största lagringsutrymme per databas (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

### <a name="premium-elastic-pool-limits-continued"></a>Premiumgränser för elastisk pool (forts.)

| eDTU:er per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Inkluderat lagringsutrymme per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Max lagringsalternativ per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Högsta OLTP-lagring i minnet per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Max antal DBs per pool <sup>1</sup> | 100 | 100 | 100 | 100 | 100 |
| Max samtidiga arbetare (begäranden) per pool <sup>2</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Max samtidiga sessioner per pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Alternativ för min eDTUs per databas | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Max val av eDTUs per databas | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Största lagringsutrymme per databas (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

> [!IMPORTANT]
> Mer än 1 TB lagringsutrymme i Premium-nivån är för närvarande tillgängligt i alla regioner utom: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD-regioner och US Government Central. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Om alla DTU:er för en elastisk pool används får varje databas i poolen lika många resurser för att bearbeta frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. En rättvis resursdelning i elastiska pooler tillämpas utöver den mängd resurser som varje databas är garanterad om det minsta antalet DTU:er per databas har angetts till ett annat värde än noll.

> [!NOTE]
> För `tempdb` begränsningar, se [tempdb gränser](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Databasegenskaper för poolade databaser

I följande tabell beskrivs egenskaperna för poolade databaser.

| Egenskap | Beskrivning |
|:--- |:--- |
| Maximalt antal eDTU:er per databas |Det högsta antalet eDTU:er som en databas i poolen kan använda, om de är tillgängliga beroende på användningen av andra databaser i poolen. Det högsta antalet eDTU:er per databas utgör ingen resursgaranti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange ett högsta antal eDTU:er per databas som är tillräckligt högt för att hantera toppar i databasanvändningen. En viss grad av övertilldelning är att förvänta eftersom poolen normalt arbetar efter ett mönster med frekvent och lågfrekvent användning av databaser där inte alla databaser har aktivitetstoppar samtidigt. Anta exempelvis att toppanvändningen per databas är 20 eDTU:er och att endast 20 % av de 100 databaserna i poolen har hög belastning samtidigt. Om det högsta antalet eDTU:er per databas har angetts till 20 eDTU:er är det rimligt att övertilldela poolen med 5 gånger så mycket och ange eDTU:erna per pool till 400. |
| Minimalt antal eDTU:er per databas |Det minsta antalet eDTU:er som en databas i poolen är garanterad. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Det minsta antalet eDTU:er per databas kan anges till 0, vilket även är standardvärdet. Den här egenskapen anges till ett värde mellan 0 och den genomsnittliga eDTU-användningen per databas. Produkten av antalet databaser i poolen och det minsta antalet eDTU:er per databas får inte överskrida antalet eDTU:er per pool. Om en pool till exempel har 20 databaser och det minsta antalet eDTU:er per databas har angetts till 10 eDTU:er så måste antalet eDTU:er per pool vara minst 200 eDTU:er. |
| Max lagringsutrymme per databas |Den maximala databasstorlek som användaren har angett för en databas i en pool. Poolade databaser delar dock allokerat poollagring. Även om det totala maxlagringsutrymmet *per databas* är inställt på att vara större än det totala tillgängliga *lagringsutrymmet i poolen,* kan det totala utrymmet som faktiskt används av alla databaser inte överskrida den tillgängliga poolgränsen.  Maximal databasstorlek avser den maximala storleken för datafilerna och omfattar inte utrymmet som används av loggfiler. |
|||

## <a name="next-steps"></a>Nästa steg

- För vCore-resursgränser för en enskild databas finns i [resursgränser för enskilda databaser med hjälp av vCore-inköpsmodellen](sql-database-vcore-resource-limits-single-databases.md)
- För DTU-resursgränser för en enskild databas finns i [resursgränser för enskilda databaser med hjälp av DTU-inköpsmodellen](sql-database-dtu-resource-limits-single-databases.md)
- För vCore-resursgränser för elastiska pooler finns i [resursgränser för elastiska pooler med hjälp av vCore-inköpsmodellen](sql-database-vcore-resource-limits-elastic-pools.md)
- För resursgränser för hanterade instanser finns i [resursbegränsningar för hanterade instanser](sql-database-managed-instance-resource-limits.md).
- Information om allmänna Azure-gränser finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resursbegränsningar på en databasserver finns i [översikt över resursbegränsningar på en SQL Database-server](sql-database-resource-limits-database-server.md) för information om begränsningar på server- och prenumerationsnivå.
