---
title: Azure SQL Database DTU-baserade gränserna | Microsoft Docs
description: Den här sidan beskrivs några vanliga DTU-baserade gränserna för Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: carlrab
ms.openlocfilehash: 9a6d347b707f9b58c5667c5d03d6374288e74267
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645131"
---
# <a name="azure-sql-database-dtu-based-resource-model-limits"></a>Azure SQL Database DTU-baserade modellen gränserna

> [!IMPORTANT]
> VCore-baserade resursbegränsningar finns [SQL-databas vCore-baserade gränserna för](sql-database-vcore-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Databas: lagringsstorlekar och prestandanivåer

Följande tabeller visar resurserna som är tillgängliga för en enskild databas vid varje servicenivå för tjänstnivå och prestandanivå för enskilda databaser. Du kan ange den tjänstnivå och prestandanivå lagringsutrymme för en enskild databas med hjälp av den [Azure-portalen](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), eller [REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="basic-service-tier"></a>Grundläggande tjänstenivå
| **Prestandanivå** | **Basic** |
| :--- | --: |
| Maximala DTU:er | 5 |
| Ingår lagringsutrymme (GB) | 2 |
| Maximalt antal lagringsalternativ (GB) | 2 |
| Maximal InMemory-OLTP-lagring (GB) |Gäller inte |
| Maximalt antal samtidiga arbetare (antal begäranden) | 30 |
| Maximalt antal samtidiga sessioner | 300 |
|||

### <a name="standard-service-tier"></a>Standardtjänstenivå
| **Prestandanivå** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Maximala DTU:er | 10 | 20 | 50 | 100 |
| Ingår lagringsutrymme (GB) | 250 | 250 | 250 | 250 |
| Maximalt antal lagringsalternativ (GB) * | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Gäller inte | Saknas | Saknas | Gäller inte |
| Maximalt antal samtidiga arbetare (antal begäranden)| 60 | 90 | 120 | 200 |
| Maximalt antal samtidiga sessioner |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard-tjänstnivå (fortsättning)
| **Prestandanivå** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Maximala DTU:er | 200 | 400 | 800 | 1600 | 3000 |
| Ingår lagringsutrymme (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximalt antal lagringsalternativ (GB) * | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Gäller inte | Saknas | Saknas | Saknas |Gäller inte |
| Maximalt antal samtidiga arbetare (antal begäranden)| 400 | 800 | 1600 | 3200 |6000 |
| Maximalt antal samtidiga sessioner |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium tjänstenivån 
| **Prestandanivå** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Maximala DTU:er | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Ingår lagringsutrymme (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maximalt antal lagringsalternativ (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Maximal InMemory-OLTP-lagring (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximalt antal samtidiga arbetare (antal begäranden)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximalt antal samtidiga sessioner | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> - Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> - Mer än 1 TB lagringsutrymme i Premium-nivån är tillgänglig i alla regioner förutom följande: Storbritannien, Nord, västra centrala USA, Storbritannien South2, Kina Öst, USDoDCentral, Tyskland Central, USDoDEast, USA Gov Southwest, oss Gov södra centrala, Tyskland nordöst, Kina Nord, oss Gov Östra. Mer omfattande tillgänglighet planeras. Det maximala lagringsutrymmet på Premium-nivån i andra regioner är begränsat till 1 TB. Se [sidan 11-15 i Aktuella begränsningar](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
>

## <a name="single-database-change-storage-size"></a>Databas: ändra lagringsstorlek

- I DTU priset för en enskild databas ingår mängden lagringsutrymme som utan extra kostnad. Extra lagring utöver det inkluderade beloppet kan etableras för en extra kostnad upp till max storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB utöver 1 TB. Inkluderade lagring belopp och max storleksgränser finns [enskild databas: lagringsstorlekar och prestandanivåer](#single-database-storage-sizes-and-performance-levels).
- Extra lagring för en enskild databas kan etableras genom att öka den maximala storleken som använder den [Azure-portalen](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), eller [REST API](/rest/api/sql/databases/update).
- Priset för extra lagring för en enskild databas är den extra lagring multiplicerat med enhetspriset extra lagring för tjänstnivån. Mer information om priserna för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Databas: ändra dtu: er

När du först välja en tjänstnivå och prestandanivå lagringsutrymmet kan du skala en enskild databas upp eller ned dynamiskt baserat på faktiska upplevelse med hjälp av den [Azure-portalen](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), eller [REST API](/rest/api/sql/databases/update). 

Följande videoklipp visar dynamiskt ändra nivå för prestanda för att öka tillgängliga dtu: er för en enskild databas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

När du ändrar servicenivån och/eller prestandanivån för en databas skapas en replik av den ursprungliga databasen på den nya prestandanivån och sedan växlas anslutningar över till repliken. Inga data förloras under den här processen men under kort tidsperiod när vi växlar över till repliken är anslutningar till databasen inaktiverade, så vissa transaktioner som sker då kan återställas. Hur lång tid för switch-over varierar, men är mindre än 30 sekunder 99% av tiden. Om det finns stora mängder transaktioner som rör sig vid anslutningar för tillfället är inaktiverade, hur lång tid för switch-over kanske inte längre. 

Hur lång tid processen att skala upp tar beror på databasens storlek och tjänstnivå före och efter ändringen. Till exempel bör en 250 GB-databas som ändras till, från eller inom en Standard-tjänstnivå slutföras inom sex timmar. Skala upp bör slutföras inom tre timmar för en databas med samma storlek som ändrar prestandanivåer inom premiumnivån.

> [!TIP]
> Om du vill övervaka i felfri drift, se: [hantera åtgärder med hjälp av REST-API SQL](/rest/api/sql/Operations/List), [hantera åtgärder med hjälp av CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) och [stoppa AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Om du uppgraderar till en högre nivå eller prestanda servicenivå ökar den maximala databasstorleken inte om du inte uttryckligen anger en större (maxsize).
* Databasutrymme används måste vara mindre än den största tillåtna storleken för mål-tjänstnivå och prestandanivå servicenivån för att nedgradera en databas. 
* När nedgradering från **Premium** till den **Standard** nivå, en extra lagringskostnaden gäller om både (1) den maximala storleken på databasen stöds i målet prestandanivå och (2) den maximala storleken överskrider den inkluderade lagringsmängden prestandanivå mål. Till exempel om en P1 databas med en maximal storlek på 500 GB downsized till S3, gäller en extra lagringskostnaden eftersom S3 stöder en maximal storlek på 500 GB och dess inkluderade lagringsutrymmet är bara 250 GB. Det extra lagringsutrymmet är därför, 500 GB – 250 GB = 250 GB. Priser för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden diskutrymme som används är mindre än den inkluderade lagringsmängden, kan sedan detta extra kostnad undvikas genom att minska den maximala databasstorleken så ingår. 
* När du uppgraderar en databas med [georeplikering](sql-database-geo-replication-portal.md) aktiverad, uppgradera de sekundära databaserna till den önskade prestandanivån innan du uppgraderar den primära databasen (allmänna riktlinjer för bästa prestanda). När du uppgraderar till en annan, krävs uppgradera den sekundära databasen först.
* När nedgradera en databas med [georeplikering](sql-database-geo-replication-portal.md) aktiverad, nedgradera primära databaserna till den önskade prestandanivån innan du nedgraderar den sekundära databasen (allmänna riktlinjer för bästa prestanda). När Nedgradera till en annan utgåva, krävs nedgradera den primära databasen först.
* Erbjudandena för återställningstjänsterna är olika för de olika tjänstnivåerna. Om du Nedgradera till den **grundläggande** nivån finns på en lägre säkerhetskopiering kvarhållningsperiod - Se [Azure SQL Database-säkerhetskopiering](sql-database-automated-backups.md).
* De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Databas: begränsningar av P11 och P15 när maximalt storleken större än 1 TB

En maximal storlek som är större än 1 TB för databas P11 och P15 stöds i följande områden: Östra Australien, sydost, södra, Kanada Central, Kanada, Öst, centrala USA, Frankrike Central, Tyskland Central, östra, västra Japan, Korea Central Norra centrala USA, Nordeuropa, södra centrala USA, Sydostasien, Storbritannien, Syd, Storbritannien, Väst, East2 USA, västra USA, USA Gov Virginia och västra Europa. Följande överväganden och begränsningar gäller för P11 och P15 databaser med en maximal storlek som är större än 1 TB:

- Om du väljer en maximal storlek som är större än 1 TB när du skapar en databas (med ett värde på 4 TB eller 4096 GB) misslyckas create-kommandot med ett fel om databasen har etablerats i en region som stöds inte.
- För befintliga P11 och P15 databaser finns i någon av regionerna som stöds, kan du öka maximalt lagringsutrymme till utöver 1 TB i steg om 256 GB upp till 4 TB. Om en större stöds i din region, Använd den [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) fungera eller kontrollera databasens storlek i Azure-portalen. Uppgradera en befintlig P11 eller P15 kan databasen endast utföras av en huvudsaklig inloggning på servernivå eller av medlemmar i rollen dbmanager-databasen. 
- Om en uppgraderingsåtgärd körs i en region som stöds uppdateras konfigurationen omedelbart. Databasen är online under uppgraderingen. Du kan dock använda fullständig mängden lagringsutrymme till mer än 1 TB lagringsutrymme förrän faktiska databasfilerna har uppgraderats till den största storleken. Hur lång tid som krävs beror på storleken på databasen som ska uppgraderas. 
- När du skapar eller uppdaterar en P11 eller P15, kan du bara välja mellan 1 TB och 4 TB maximal storlek i steg om 256 GB. När du skapar en P11/P15 är standardalternativet för lagring på 1 TB förvald. Du kan öka lagring maximalt till högst 4 TB för en ny eller befintlig enskild databas för databaser som finns på någon av regionerna som stöds. Den maximala storleken kan inte ökas ovan 1 TB för alla andra regioner. Priset ändras inte när du väljer 4 TB ingår lagringsutrymme.
- Om den maximala storleken för en databas är större än 1 TB, kan sedan den inte ändras till 1 TB även om lagringsutrymme som faktiskt används understiger 1 TB. Därför går inte att det nedgradera en P11 eller P15 med en maximal storlek som är större än 1 TB till 1 TB P11 eller 1 TB P15 eller lägre prestanda-nivån, till exempel P1 P6). Den här begränsningen gäller även återställning och kopiera scenarier inklusive i tidpunkt, geo-återställning, lång-sikt-säkerhetskopia-kvarhållning och databaskopian. När en databas har konfigurerats med en maximal storlek som är större än 1 TB kan måste alla återställningsåtgärder för den här databasen köras i en P11/P15 med en maximal storlek som är större än 1 TB.
- Scenarier med aktiv geo-replikering:
   - Skapa en relation för geo-replikering: om den primära databasen är P11 eller P15, secondary(ies) måste också vara P11 eller P15; lägre prestandanivåer avvisas som sekundärservrar eftersom de inte kan stödja fler än 1 TB.
   - Uppgradera den primära databasen i en relation för geo-replikering: ändra den maximala storleken till mer än 1 TB på en primär databas utlöser samma ändringar på den sekundära databasen. Båda uppgraderingarna måste genomföras för att ändringarna på primärt ska börja gälla. Region begränsningar för alternativet mer än 1 TB. Om sekundärt finns i en region som inte stöder fler än 1 TB, uppgraderas inte den primära servern.
- Med hjälp av tjänsten Import/Export för att läsa in P11/P15 databaser med mer än 1 TB stöds inte. Använda SqlPackage.exe till [importera](sql-database-import.md) och [exportera](sql-database-export.md) data.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastisk pool: lagringsstorlekar och prestandanivåer

Följande tabeller visar de tillgängliga resurserna på varje servicenivå för tjänstnivå och prestandanivå för elastiska pooler SQL-databas. Du kan ange tjänstnivån, prestanda och storage belopp med den [Azure-portalen](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), eller [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Resurs-gränserna för enskilda databaser i elastiska pooler är vanligtvis samma som för enskilda databaser utanför pooler baserat på dtu: er och tjänstnivån. Högsta antal samtidiga arbetare för en S2-databas är till exempel 120 arbetare. Högsta antal samtidiga arbetare för en databas i en Standardpool är därför också 120 arbetare om max DTU per databas i poolen är 50 dtu: er (som motsvarar S2).

### <a name="basic-elastic-pool-limits"></a>Gränser för grundläggande elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Ingår lagringsutrymme per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximalt antal lagringsalternativ per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas | Gäller inte |
| Maximalt antal databaser per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximalt antal samtidiga arbetare (begäranden) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Alternativ för min edtu: er per databas | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Alternativ för max edtu: er per databas | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Största lagringsutrymme per databas (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Standardgränser för elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Ingår lagringsutrymme per pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Maximalt antal lagringsalternativ per pool (GB) * | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Saknas | Gäller inte | 
| Maximalt antal databaser per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Alternativ för min edtu: er per databas | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Alternativ för max edtu: er per databas | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Maximalt lagringsutrymme per databas (GB)* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standardgränser för elastisk pool (forts.) 

| eDTU:er per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Ingår lagringsutrymme per pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Maximalt antal lagringsalternativ per pool (GB) * | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Gäller inte | 
| Maximalt antal databaser per pool | 500 | 500 | 500 | 500 | 500 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Alternativ för min edtu: er per databas | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Alternativ för max edtu: er per databas | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Maximalt antal lagringsalternativ per databas (GB) * | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Gränser för Premium elastisk pool

| eDTU:er per pool | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Ingår lagringsutrymme per pool (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Maximalt antal lagringsalternativ per pool (GB) * | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | 1 | 2 | 4 | 10 | 12 | 
| Maximalt antal databaser per pool | 50 | 100 | 100 | 100 | 100 | 
| Maximalt antal samtidiga arbetare per pool (begäranden) | 200 | 400 | 800 | 1600 | 2400 | 
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Minimalt antal eDTU:er per databas | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Maximalt antal eDTU:er per databas | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maximalt lagringsutrymme per databas (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Premiumgränser för elastisk pool (forts.) 

| eDTU:er per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Ingår lagringsutrymme per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximalt antal lagringsalternativ per pool (GB) * | 2048 | 2560 | 3072 | 3548 | 4096|
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximalt antal databaser per pool | 100 | 100 | 100 | 100 | 100 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Alternativ för min edtu: er per databas | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Alternativ för max edtu: er per databas | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Maximalt lagringsutrymme per databas (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> - Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [SQL-databas sida med priser](https://azure.microsoft.com/pricing/details/sql-database/). Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [SQL-databas sida med priser](https://azure.microsoft.com/pricing/details/sql-database/).
>
> - Mer än 1 TB lagringsutrymme i Premium-nivån är tillgänglig i alla regioner förutom följande: Storbritannien, Nord, västra centrala USA, Storbritannien South2, Kina Öst, USDoDCentral, Tyskland Central, USDoDEast, USA Gov Southwest, oss Gov södra centrala, Tyskland nordöst, Kina Nord, oss Gov Östra. Mer omfattande tillgänglighet planeras. Det maximala lagringsutrymmet på Premium-nivån i andra regioner är begränsat till 1 TB. Se [sidan 11-15 i Aktuella begränsningar](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
>

Om alla DTU:er för en elastisk pool används får varje databas i poolen lika många resurser för att bearbeta frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. En rättvis resursdelning i elastiska pooler tillämpas utöver den mängd resurser som varje databas är garanterad om det minsta antalet DTU:er per databas har angetts till ett annat värde än noll.

### <a name="database-properties-for-pooled-databases"></a>Databasegenskaper för grupperade databaser

I följande tabell beskrivs egenskaperna för pool-databaser.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Maximalt antal eDTU:er per databas |Det högsta antalet eDTU:er som en databas i poolen kan använda, om de är tillgängliga beroende på användningen av andra databaser i poolen. Det högsta antalet eDTU:er per databas utgör ingen resursgaranti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange ett högsta antal eDTU:er per databas som är tillräckligt högt för att hantera toppar i databasanvändningen. En viss grad av övertilldelning är att förvänta eftersom poolen normalt arbetar efter ett mönster med frekvent och lågfrekvent användning av databaser där inte alla databaser har aktivitetstoppar samtidigt. Anta exempelvis att toppanvändningen per databas är 20 eDTU:er och att endast 20 % av de 100 databaserna i poolen har hög belastning samtidigt. Om det högsta antalet eDTU:er per databas har angetts till 20 eDTU:er är det rimligt att övertilldela poolen med 5 gånger så mycket och ange eDTU:erna per pool till 400. |
| Minimalt antal eDTU:er per databas |Det minsta antalet eDTU:er som en databas i poolen är garanterad. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Det minsta antalet eDTU:er per databas kan anges till 0, vilket även är standardvärdet. Den här egenskapen anges till ett värde mellan 0 och den genomsnittliga eDTU-användningen per databas. Produkten av antalet databaser i poolen och det minsta antalet eDTU:er per databas får inte överskrida antalet eDTU:er per pool. Om en pool till exempel har 20 databaser och det minsta antalet eDTU:er per databas har angetts till 10 eDTU:er så måste antalet eDTU:er per pool vara minst 200 eDTU:er. |
| Maximalt lagringsutrymme per databas |Den maximala databasstorleken anges av användaren för en databas i en pool. Dock dela grupperade databaser allokerade poolens lagringsutrymme. Även om totala maximal lagringskapacitet **per databas* anges måste vara större än det totala tillgängliga lagringsutrymmet **utrymmet i poolen*, det totala utrymmet som faktiskt används av alla databaserna inte överskrida den tillgängliga poolen gräns. Max databasens storlek avser den maximala storleken för datafiler och innehåller inte det utrymme som används av loggfiler. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Elastisk pool: ändra lagringsstorlek

- EDTU priset för en elastisk pool innehåller en viss mängd lagring utan extra kostnad. Extra lagring utöver det inkluderade beloppet kan etableras för en extra kostnad upp till max storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB utöver 1 TB. Inkluderade lagring belopp och max storleksgränser finns [elastisk pool: lagringsstorlekar och prestandanivåer](#elastic-pool-storage-sizes-and-performance-levels).
- Extra lagring för en elastisk pool kan etableras genom att öka den maximala storleken som använder den [Azure-portalen](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [REST API ](/rest/api/sql/elasticpools/update).
- Priset för extra lagring för en elastisk pool är den extra lagring multiplicerat med enhetspriset extra lagring för tjänstnivån. Mer information om priserna för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Elastisk pool: ändra edtu: er

Du kan öka eller minska resurserna som är tillgängliga för en elastisk pool utifrån behov med hjälp av den [Azure-portalen](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [ REST-API](/rest/api/sql/elasticpools/update).

- När rescaling pool-edtu: er har en kort databasanslutningar släpps. Det här är samma beteende som inträffar när rescaling dtu: er för en enskild databas (inte i en pool). Mer information om tidsåtgång och effekt av borttagna anslutningar för en databas under rescaling åtgärder finns [Rescaling dtu: er för en enskild databas](#single-database-change-storage-size). 
- Varaktigheten för skala om pool-edtu: er kan vara beroende av den totala mängden lagringsutrymme som används av alla databaser i poolen. I allmänhet rescaling svarstiden medelvärden 90 minuter eller mindre per 100 GB. Om det totala utrymmet som används av alla databaser i poolen är exempelvis 200 GB och den förväntade svarstiden för rescaling poolen är 3 timmar eller mindre. I vissa fall i Standard- eller Basic-nivån vara rescaling fördröjningen under 5 minuter, oavsett hur mycket diskutrymme som används.
- I allmänhet varaktighet ändra min edtu: er per databas eller max edtu: er per databas är fem minuter eller mindre.
- När downsizing pool-edtu: er måste poolutrymmet används vara mindre än den största tillåtna storleken för den mål service nivå och pool-edtu: er.
- När rescaling pool-edtu: er, gäller en extra lagringskostnaden om (1) den maximala lagringsstorleken för poolen stöds av målpoolen och (2) den maximala lagringsstorleken överskrider inkluderade lagring målpoolen. Till exempel om en 100 eDTU Standardpool med en maximal storlek på 100 GB downsized till en 50 eDTU Standardpool, gäller en extra lagringskostnaden eftersom målpool stöder en maximal storlek på 100 GB och dess inkluderade lagringsutrymmet är bara 50 GB. Därför är det extra lagringsutrymmet 100 GB – 50 GB = 50 GB. Priser för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden diskutrymme som används är mindre än den inkluderade lagringsmängden, kan sedan detta extra kostnad undvikas genom att minska den maximala databasstorleken så ingår. 

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Vad är det maximala antalet servrar och databaser?

| Maximal | Värde |
| :--- | :--- |
| Databaser per server | 5000 |
| Antal servrar per prenumeration per region | 20 |
|||

> [!IMPORTANT]
> När antalet databaser som närmar sig gränsen per server, kan följande inträffa:
> <br> • Öka svarstid i kör frågor mot master-databasen.  Detta inkluderar vyer av användningsstatistik för resurs, till exempel sys.resource_stats.
> <br> • Ökad latens i hanteringsåtgärder och återgivning portal åsikter som rör uppräkning databaser på servern.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Vad händer när databasen och gränserna för elastiska poolen nås?

### <a name="compute-dtus-and-edtus"></a>Beräkna (dtu: er och edtu: er)

När databasen beräkning användning (mätt genom dtu: er och edtu: er) är hög, svarstid ökar och kan även timeout. Under dessa förhållanden frågor i kö av tjänsten och tillhandahålls resurser för körning som resursen bli ledig.
När den påträffar hög beräkning användning inkluderar minskning alternativ:

- Öka prestandanivåerna för databasen eller elastisk pool för att ge databasen mer dtu: er eller edtu: er. Se [enskild databas: ändra dtu: er](#single-database-change-dtus) och [elastisk pool: ändra edtu: er](#elastic-pool-change-edtus).
- Optimera frågor för att minska resursanvändningen för varje fråga. Mer information finns i [frågan finjustera/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

När databasutrymme används når den maximala storleksgränsen, infogas i databasen och uppdateringar som ökar storleken på data misslyckas och klienter tar emot en [felmeddelande](sql-database-develop-error-messages.md). Databasen väljer och BORTTAGNINGAR fortsätter att fungera.

När den påträffar hög användningen inkluderar minskning alternativ:

- Öka max storleken på databasen eller elastisk pool eller ändra prestandanivå att få mer inkluderade lagring. Se [gränserna för SQL-databas](sql-database-dtu-resource-limits.md).
- Om databasen är i en elastisk pool, kan sedan också databasen flyttas utanför poolen så att dess lagringsutrymme inte delas med andra databaser.

### <a name="sessions-and-workers-requests"></a>Sessioner och personer (antal begäranden) 

Det maximala antalet sessioner och personer bestäms av tjänstnivå och prestandanivå servicenivån (dtu: er och edtu: er). Nya begäranden avvisas när sessionen eller worker gränser och klienter får ett felmeddelande. När antalet anslutningar som är tillgängliga kan styras av programmet, är antalet samtidiga arbetare ofta svårare att uppskatta och styra. Detta gäller särskilt under belastningsperioder när gränserna för databasen har uppnåtts och arbetare växer på grund av längre frågor som körs. 

När den påträffar hög session eller worker användning inkluderar minskning alternativ:
- Öka servicenivå för nivå eller prestanda för databasen eller elastisk pool. Se [enskild databas: ändra lagringsstorlek](#single-database-change-storage-size), [enskild databas: ändra dtu: er](#single-database-change-dtus), [elastisk pool: ändra lagringsstorlek](#elastic-pool-change-storage-size), och [elastisk pool: ändra edtu: er ](#elastic-pool-change-edtus).
- Optimera frågor för att minska resursanvändningen för varje fråga om orsaken till ökad worker belastningen beror på konkurrens om beräkningsresurser. Mer information finns i [frågan finjustera/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Nästa steg

- Se [SQL-databasen med vanliga frågor om](sql-database-faq.md) för svar på vanliga frågor och svar.
- Information om allmänna Azure gränser finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om dtu: er och edtu: er finns [dtu: er och edtu: er](sql-database-what-is-a-dtu.md).
- Information om storleksgränser för tempdb finns https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
