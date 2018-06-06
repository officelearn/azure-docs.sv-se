---
title: Azure SQL Database vCore-baserade gränserna | Microsoft Docs
description: Den här sidan beskrivs några vanliga vCore-baserade gränserna för Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: carlrab
ms.openlocfilehash: 324f9f35de37c717d57e46413cd881dce785f4fd
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737671"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Azure SQL Database vCore-baserade köp modellen gränser (förhandsgranskning)

> [!IMPORTANT]
> DTU-baserade gränser för köp modellen finns [SQL Database DTU-baserade gränserna för](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Databas: lagringsstorlekar och prestandanivåer

Följande tabeller visar resurserna som är tillgängliga för en enskild databas vid varje servicenivå för tjänstnivå och prestandanivå för enskilda databaser. Du kan ange den tjänstnivå och prestandanivå lagringsutrymme för en enskild databas med hjälp av den [Azure-portalen](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), eller [REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="general-purpose-service-tier"></a>Tjänstnivån för allmänna ändamål

#### <a name="generation-4-compute-platform"></a>Generation 4 beräkningsplattform
|Prestandanivå|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (CA)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|
|Maxstorlek för data (GB)|1024|1024|1536|3072|4096|4096|
|Maxstorlek|307|307|461|922|1229|1229|
|TempDB size(DB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Maximalt antal samtidiga arbetare (antal begäranden)|200|400|800|1600|3200|4800|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|000
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

#### <a name="generation-5-compute-platform"></a>Generation 5 beräkningsplattform
|Prestandanivå|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (CA)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|
|Maxstorlek för data (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Maxstorlek|307|307|461|614|1229|1229|1229|1229|
|TempDB size(DB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maximalt antal samtidiga arbetare (antal begäranden)|200|400|800|1600|2400|3200|4000|8000|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|1|1|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

### <a name="business-critical-service-tier"></a>Kritisk tjänst affärsnivå

#### <a name="generation-4-compute-platform"></a>Generation 4 beräkningsplattform
|Prestandanivå|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|1|2|4|8|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maxstorlek för data (GB)|1024|1024|1024|1024|1024|1024|
|Maxstorlek|307|307|307|307|307|307|
|TempDB size(DB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|I/o-svarstid (CA)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Maximalt antal samtidiga arbetare (antal begäranden)|200|400|800|1600|3200|4800|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|3|3|3|3|3|3|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Ja|Ja|Ja|Ja|Ja|Ja|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

#### <a name="generation-5-compute-platform"></a>Generation 5 beräkningsplattform
|Prestandanivå|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (CA)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Maxstorlek för data (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maxstorlek|307|307|307|307|614|1229|1229|1229|
|TempDB size(DB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Maximalt antal samtidiga arbetare (antal begäranden)|200|400|800|1600|2400|3200|4000|8000|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|3|3|3|3|3|3|3|3|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

## <a name="single-database-change-storage-size"></a>Databas: ändra lagringsstorlek

- Lagring kan etableras upp till den maximala storleksgränsen med steg om 1 GB. Den minsta konfigurerbara datalagringen är 5GB 
- Lagringsutrymme för en enskild databas kan etableras med ökar eller minskar den maximala storleken som använder den [Azure-portalen](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), eller [REST API](/rest/api/sql/databases/update).
- SQL-databasen tilldelas automatiskt 30% av ytterligare lagringsutrymme för loggfiler och 32GB per vCore för TempDB, men inte överstiga 384GB. TempDB finns på en ansluten SSD i alla servicenivåer.
- Priset för lagring för en enskild databas är summan av data lagring och logg lagring belopp multiplicerat med lagring enhetspriset för tjänstnivån. Kostnaden för TempDB ingår i vCore pris. Mer information om priserna för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-vcores"></a>Databas: ändra vCores

När du först välja antalet vCores kan du skala en enskild databas upp eller ned dynamiskt baserat på faktiska upplevelse med hjälp av den [Azure-portalen](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), eller [REST API](/rest/api/sql/databases/update). 

När du ändrar servicenivån och/eller prestandanivån för en databas skapas en replik av den ursprungliga databasen på den nya prestandanivån och sedan växlas anslutningar över till repliken. Inga data förloras under den här processen men under kort tidsperiod när vi växlar över till repliken är anslutningar till databasen inaktiverade, så vissa transaktioner som sker då kan återställas. Hur lång tid för switch-over varierar, men är vanligtvis under 4 sekunder är mindre än 30 sekunder 99% av tiden. Om det finns stora mängder transaktioner som rör sig vid anslutningar för tillfället är inaktiverade, hur lång tid för switch-over kanske inte längre. 

Hur lång tid processen att skala upp tar beror på databasens storlek och tjänstnivå före och efter ändringen. Till exempel bör en 250 GB-databas som ändras till, från eller inom en generella tjänstnivå slutföras inom sex timmar. Skala upp bör slutföras inom tre timmar för en databas med samma storlek som ändrar prestandanivåer inom företag kritiska tjänstnivån.

> [!TIP]
> Om du vill övervaka i felfri drift, se: [hantera åtgärder med hjälp av REST-API SQL](/rest/api/sql/Operations/List), [hantera åtgärder med hjälp av CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) och [stoppa AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Om du uppgraderar till en högre nivå eller prestanda servicenivå ökar den maximala databasstorleken inte om du inte uttryckligen anger en större (maxsize).
* Databasutrymme används måste vara mindre än den största tillåtna storleken för mål-tjänstnivå och prestandanivå servicenivån för att nedgradera en databas. 
* När du uppgraderar en databas med [georeplikering](sql-database-geo-replication-portal.md) aktiverad, uppgradera de sekundära databaserna till den önskade prestandanivån innan du uppgraderar den primära databasen (allmänna riktlinjer för bästa prestanda). När du uppgraderar till en annan, krävs uppgradera den sekundära databasen först.
* När nedgradera en databas med [georeplikering](sql-database-geo-replication-portal.md) aktiverad, nedgradera primära databaserna till den önskade prestandanivån innan du nedgraderar den sekundära databasen (allmänna riktlinjer för bästa prestanda). När Nedgradera till en annan utgåva, krävs nedgradera den primära databasen först.
* De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastisk pool: lagringsstorlekar och prestandanivåer

Följande tabeller visar de tillgängliga resurserna på varje servicenivå för tjänstnivå och prestandanivå för elastiska pooler SQL-databas. Du kan ange tjänstnivån, prestanda och storage belopp med den [Azure-portalen](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), eller [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Resurs-gränserna för enskilda databaser i elastiska pooler är vanligtvis samma som för enskilda databaser utanför pooler som har samma prestanda. Högsta antal samtidiga arbetare för en GP_Gen4_1 databas är till exempel 200 anställda. Högsta antal samtidiga arbetare för en databas i en pool med GP_Gen4_1 är därför också 200 anställda. Observera att det totala antalet samtidiga arbetare i GP_Gen4_1 poolen är 210.

### <a name="general-purpose-service-tier"></a>Tjänstnivån för allmänna ändamål

#### <a name="generation-4-compute-platform"></a>Generation 4 beräkningsplattform
|Prestandanivå|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|Maxstorlek för data (GB)|512|756|1536|2048|3584|4096|
|Maxstorlek|154|227|461|614|1075|1229|
|TempDB size(DB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|I/o-svarstid (CA)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|
|Maximalt antal samtidiga arbetare (antal begäranden)|210|420|840|1680|3360|5040|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Max poolen densitet|100|200|500|500|500|500|
|Minsta/högsta elastisk pool Klicka-slutar|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Antal repliker|1|1|1|1|1|1|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

#### <a name="generation-5-compute-platform"></a>Generation 5 beräkningsplattform
|Prestandanivå|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|Maxstorlek för data (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Maxstorlek|154|227|461|614|922|1229|1229|1229|
|TempDB size(DB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|I/o-svarstid (CA)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|
|Maximalt antal samtidiga arbetare (antal begäranden)|210|420|840|1680|2520|3360|4200|8400
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Max poolen densitet|100|200|500|500|500|500|500|500|
|Minsta/högsta elastisk pool Klicka-slutar|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Antal repliker|1|1|1|1|1|1|1|1|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

### <a name="business-critical-service-tier"></a>Kritisk tjänst affärsnivå

#### <a name="generation-4-compute-platform"></a>Generation 4 beräkningsplattform
|Prestandanivå|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|1|2|4|8|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maxstorlek för data (GB)|1024|1024|1024|1024|1024|1024|
|Maxstorlek|307|307|307|307|307|307|
|TempDB size(DB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|I/o-svarstid (CA)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Maximalt antal samtidiga arbetare (antal begäranden)|210|420|840|1680|3360|5040|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Max poolen densitet|Gäller inte|50|100|100|100|100|
|Minsta/högsta elastisk pool Klicka-slutar|Gäller inte|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Antal repliker|3|3|3|3|3|3|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Ja|Ja|Ja|Ja|Ja|Ja|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

#### <a name="generation-5-compute-platform"></a>Generation 5 beräkningsplattform
|Prestandanivå|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (CA)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Maxstorlek för data (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maxstorlek|307|307|307|307|614|1229|1229|1229|
|TempDB size(DB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Maximalt antal samtidiga arbetare (antal begäranden)|210|420|840|1680|2520|3360|5040|8400|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Max poolen densitet|Gäller inte|50|100|100|100|100|100|100|
|Minsta/högsta elastisk pool Klicka-slutar|Gäller inte|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Antal repliker|3|3|3|3|3|3|3|3|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

Om alla vCores för en elastisk pool är upptagna, får varje databas i poolen lika mycket beräkningsresurser för bearbetning av frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. Elastisk pool resursdelning skälighet är förutom valfritt antal resurs annars garanterat varje databas när vCore minsta per databas har angetts till ett värde som inte är noll.

### <a name="database-properties-for-pooled-databases"></a>Databasegenskaper för grupperade databaser

I följande tabell beskrivs egenskaperna för pool-databaser.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Max vCores per databas |Det maximala antalet vCores som alla databaser i poolen kan använda om tillgängliga baserat på användning av andra databaser i poolen. Max vCores per databas är inte en resurs garanti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange högsta vCores per databas som är tillräckligt högt för att hantera toppar i databasen användning. En viss grad av övertilldelning är att förvänta eftersom poolen normalt arbetar efter ett mönster med frekvent och lågfrekvent användning av databaser där inte alla databaser har aktivitetstoppar samtidigt.|
| Min vCores per databas |Det minsta antalet vCores som garanterat alla databaser i poolen. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Min vCores per databas kan anges till 0 och även är standardvärdet. Den här egenskapen anges till var som helst mellan 0 och genomsnittlig vCores användning per databas. Produkten av antalet databaser i poolen och min vCores per databas får inte överskrida vCores per pool.|
| Maximalt lagringsutrymme per databas |Den maximala databasstorleken anges av användaren för en databas i en pool. Grupperade databaser dela allokerade poolen lagring, så att en databas kan nå storlek är begränsad till mindre återstående poolens lagringsutrymme och databasstorlek. Max databasens storlek avser den maximala storleken för datafiler och innehåller inte det utrymme som används av loggfiler. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Elastisk pool: ändra lagringsstorlek

- Lagring kan du konfigurera upp till den maximala storleksgränsen: 
 - För standardlagring, öka eller minska storleken i steg 10 GB
 - För Premium-lagring, öka eller minska storleken i steg om 250 GB
- Lagringsutrymme för en elastisk pool kan etableras med ökar eller minskar den maximala storleken som använder den [Azure-portalen](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [ REST-API](/rest/api/sql/elasticpools/update).
- Priset för lagring för en elastisk pool är lagringsmängden multiplicerat med lagring enhetspriset för tjänstnivån. Mer information om priserna för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-vcores"></a>Elastisk pool: ändra vCores

Du kan öka eller minska prestandanivån uppdateras till en elastisk pool utifrån behov med hjälp av den [Azure-portalen](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [REST API](/rest/api/sql/elasticpools/update).

- När rescaling pool vCores släpps kort databasanslutningar. Det här är samma beteende som inträffar när rescaling dtu: er för en enskild databas (inte i en pool). Mer information om tidsåtgång och effekt av borttagna anslutningar för en databas under rescaling åtgärder finns [Rescaling dtu: er för en enskild databas](#single-database-change-storage-size). 
- Varaktigheten för skala om poolen vCores kan vara beroende av den totala mängden lagringsutrymme som används av alla databaser i poolen. I allmänhet rescaling svarstiden medelvärden 90 minuter eller mindre per 100 GB. Om det totala utrymmet som används av alla databaser i poolen är exempelvis 200 GB och den förväntade svarstiden för rescaling poolen är 3 timmar eller mindre. I vissa fall i Standard- eller Basic-nivån vara rescaling fördröjningen under 5 minuter, oavsett hur mycket diskutrymme som används.
- I allmänhet varaktighet ändra min vCores per databas eller max vCores per databas är fem minuter eller mindre.
- När downsizing pool vCores måste poolutrymmet används vara mindre än den största tillåtna storleken för tjänsten mål vCores nivå och pool.

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

### <a name="compute-vcores"></a>Beräkna (vCores)

När databasen beräkning användning (mätt genom användning av vCore) är hög, svarstid ökar och kan även timeout. Under dessa förhållanden frågor i kö av tjänsten och tillhandahålls resurser för körning som resursen bli ledig.
När den påträffar hög beräkning användning inkluderar minskning alternativ:

- Öka prestandanivåerna för databasen eller elastisk pool för att ge mer vCores databasen. Se [enskild databas: ändra cVcores](#single-database-change-vcores) och [elastisk pool: ändra vCores](#elastic-pool-change-vcores).
- Optimera frågor för att minska resursanvändningen för varje fråga. Mer information finns i [frågan finjustera/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

När databasutrymme används når den maximala storleksgränsen, infogas i databasen och uppdateringar som ökar storleken på data misslyckas och klienter tar emot en [felmeddelande](sql-database-develop-error-messages.md). Databasen väljer och BORTTAGNINGAR fortsätter att fungera.

När den påträffar hög användningen inkluderar minskning alternativ:

- Öka max storleken på databasen eller elastisk pool eller ändra prestandanivå att öka det maximalt lagringsutrymme. Se [SQL-databas vCore-baserade gränserna för](sql-database-vcore-resource-limits.md).
- Om databasen är i en elastisk pool, kan sedan också databasen flyttas utanför poolen så att dess lagringsutrymme inte delas med andra databaser.

### <a name="sessions-and-workers-requests"></a>Sessioner och personer (antal begäranden) 

Det maximala antalet sessioner och personer bestäms av servicenivå tjänstnivå och prestandanivå. Nya begäranden avvisas när sessionen eller worker gränser och klienter får ett felmeddelande. När antalet anslutningar som är tillgängliga kan styras av programmet, är antalet samtidiga arbetare ofta svårare att uppskatta och styra. Detta gäller särskilt under belastningsperioder när gränserna för databasen har uppnåtts och arbetare växer på grund av längre frågor som körs. 

När den påträffar hög session eller worker användning inkluderar minskning alternativ:
- Öka servicenivå för nivå eller prestanda för databasen eller elastisk pool. Se [enskild databas: ändra lagringsstorlek](#single-database-change-storage-size), [enskild databas: ändra vCores](#single-database-change-vcores), [elastisk pool: ändra lagringsstorlek](#elastic-pool-change-storage-size), och [elastisk pool: ändra vCores ](#elastic-pool-change-vcores).
- Optimera frågor för att minska resursanvändningen för varje fråga om orsaken till ökad worker belastningen beror på konkurrens om beräkningsresurser. Mer information finns i [frågan finjustera/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Nästa steg

- Se [SQL-databasen med vanliga frågor om](sql-database-faq.md) för svar på vanliga frågor och svar.
- Information om allmänna Azure gränser finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).
