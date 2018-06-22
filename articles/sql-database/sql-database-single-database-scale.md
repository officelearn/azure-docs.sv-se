---
title: Skala en enskild databas - resurser i Azure SQL Database | Microsoft Docs
description: Den här artikeln beskriver hur du kan skala beräkning och lagring-resurser tillgängliga för en enskild databas i Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 525416506a22f386de574ca02b2e919ac47b8737
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309885"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skala en enskild databasresurser i Azure SQL Database

Den här artikeln beskriver hur du kan skala beräkning och lagring-resurser tillgängliga för en enskild databas i Azure SQL Database. 

## <a name="vcore-based-purchasing-model-change-storage-size"></a>vCore-baserade inköpsmodell: ändra lagringsstorlek

- Lagring kan etableras upp till den maximala storleksgränsen med steg om 1 GB. Den minsta konfigurerbara datalagringen är 5GB 
- Lagringsutrymme för en enskild databas kan etableras med ökar eller minskar den maximala storleken som använder den [Azure-portalen](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), eller [REST API](/rest/api/sql/databases/update).
- SQL-databasen tilldelas automatiskt 30% av ytterligare lagringsutrymme för loggfiler och 32GB per vCore för TempDB, men inte överstiga 384GB. TempDB finns på en ansluten SSD i alla servicenivåer.
- Priset för lagring för en enskild databas är summan av data lagring och logg lagring belopp multiplicerat med lagring enhetspriset för tjänstnivån. Kostnaden för TempDB ingår i vCore pris. Mer information om priserna för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>vCore-baserade inköpsmodell: ändra beräkningsresurser

När du först välja antalet vCores kan du skala en enskild databas upp eller ned dynamiskt baserat på faktiska upplevelse med hjälp av den [Azure-portalen](sql-database-single-database-scale.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), eller [REST API](/rest/api/sql/databases/update). 

När du ändrar servicenivån och/eller prestandanivån för en databas skapas en replik av den ursprungliga databasen på den nya prestandanivån och sedan växlas anslutningar över till repliken. Inga data förloras under den här processen men under kort tidsperiod när vi växlar över till repliken är anslutningar till databasen inaktiverade, så vissa transaktioner som sker då kan återställas. Hur lång tid för switch-over varierar, men är vanligtvis under 4 sekunder är mindre än 30 sekunder 99% av tiden. Om det finns stora mängder transaktioner som rör sig vid anslutningar för tillfället är inaktiverade, hur lång tid för switch-over kanske inte längre. 

Hur lång tid processen att skala upp tar beror på databasens storlek och tjänstnivå före och efter ändringen. Till exempel bör en 250 GB-databas som ändras till, från eller inom en generella tjänstnivå slutföras inom sex timmar. Skala upp bör slutföras inom tre timmar för en databas med samma storlek som ändrar prestandanivåer inom företag kritiska tjänstnivån.

> [!TIP]
> Om du vill övervaka i felfri drift, se: [hantera åtgärder med hjälp av REST-API SQL](/rest/api/sql/Operations/List), [hantera åtgärder med hjälp av CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) och [stoppa AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Om du uppgraderar till en högre nivå eller prestanda servicenivå ökar den maximala databasstorleken inte om du inte uttryckligen anger en större (maxsize).
* Databasutrymme används måste vara mindre än den största tillåtna storleken för mål-tjänstnivå och prestandanivå servicenivån för att nedgradera en databas. 
* När du uppgraderar en databas med [georeplikering](sql-database-geo-replication-portal.md) aktiverad, uppgradera de sekundära databaserna till den önskade prestandanivån innan du uppgraderar den primära databasen (allmänna riktlinjer för bästa prestanda). När du uppgraderar till en annan, krävs uppgradera den sekundära databasen först.
* När nedgradera en databas med [georeplikering](sql-database-geo-replication-portal.md) aktiverad, nedgradera primära databaserna till den önskade prestandanivån innan du nedgraderar den sekundära databasen (allmänna riktlinjer för bästa prestanda). När Nedgradera till en annan utgåva, krävs nedgradera den primära databasen först.
* De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>DTU-baserade inköpsmodell: ändra lagringsstorlek

- I DTU priset för en enskild databas ingår mängden lagringsutrymme som utan extra kostnad. Extra lagring utöver det inkluderade beloppet kan etableras för en extra kostnad upp till max storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB utöver 1 TB. Inkluderade lagring belopp och max storleksgränser finns [enskild databas: lagringsstorlekar och prestandanivåer](#single-database-storage-sizes-and-performance-levels).
- Extra lagring för en enskild databas kan etableras genom att öka den maximala storleken som använder den [Azure-portalen](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), eller [REST API](/rest/api/sql/databases/update).
- Priset för extra lagring för en enskild databas är den extra lagring multiplicerat med enhetspriset extra lagring för tjänstnivån. Mer information om priserna för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>DTU-baserade inköpsmodell: ändra beräkningsresurser (Dtu)

När du först välja en tjänstnivå och prestandanivå lagringsutrymmet kan du skala en enskild databas upp eller ned dynamiskt baserat på faktiska upplevelse med hjälp av den [Azure-portalen](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), eller [REST API](/rest/api/sql/databases/update). 

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

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>DTU-baserade inköpsmodell: begränsningar av P11 och P15 när maximalt storleken större än 1 TB

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


## <a name="next-steps"></a>Nästa steg

Övergripande resursbegränsningar finns [SQL-databas vCore-baserade gränserna - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [SQL Database DTU-baserade gränserna - elastiska pooler](sql-database-dtu-resource-limits-single-databases.md).
