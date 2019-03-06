---
title: Azure SQL Database DTU-baserad resurs begränsar enskilda databaser | Microsoft Docs
description: Den här sidan beskriver några vanliga DTU-baserade resursbegränsningar för enskilda databaser i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: 0a61656def7e8ddfab227eb392ed14307dbf189d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433101"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Resursgränser för enskilda databaser med hjälp av den DTU-baserade inköpsmodellen

Den här artikeln innehåller detaljerade resursgränser för enskilda databaser i Azure SQL Database med hjälp av den DTU-baserade inköpsmodellen.

DTU-baserade inköpschef modellen resursgränser för elastiska pooler, se [DTU-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). VCore-baserade resursbegränsningar finns [vCore-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). Läs mer om de olika modellerna för inköp, [köpa modeller och tjänstnivåer](sql-database-purchase-models.md).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Databas: Lagringsstorlekar och storlekar

Följande tabeller visar resurserna som är tillgängliga för en enskild databas på varje tjänstnivå och beräkna storleken. Du kan ange tjänstnivå, beräkningsstorleken och lagringsutrymmet för en enkel databas med hjälp av den [Azure-portalen](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [ Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), eller [REST-API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

### <a name="basic-service-tier"></a>Grundläggande tjänstenivå

| **Beräkna storlek** | **Basic** |
| :--- | --: |
| Maximala DTU:er | 5 |
| Lagringsutrymme (GB) | 2 |
| Maximalt antal lagringsalternativ (GB) | 2 |
| Maximal InMemory-OLTP-lagring (GB) |Gäller inte |
| Maximalt antal samtidiga arbetare (begäranden) | 30 |
| Maximalt antal samtidiga sessioner | 300 |
|||

### <a name="standard-service-tier"></a>Standardtjänstenivå

| **Beräkna storlek** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximala DTU:er | 10 | 20 | 50 | 100 |
| Lagringsutrymme (GB) | 250 | 250 | 250 | 250 |
| Maximalt antal lagringsalternativ (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Gäller inte | Saknas | Saknas | Gäller inte |
| Maximalt antal samtidiga arbetare (begäranden)| 60 | 90 | 120 | 200 |
| Maximalt antal samtidiga sessioner |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard-tjänstnivå (forts.)

| **Beräkna storlek** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximala DTU:er | 200 | 400 | 800 | 1600 | 3000 |
| Lagringsutrymme (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximalt antal lagringsalternativ (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Gäller inte | Saknas | Saknas | Saknas |Gäller inte |
| Maximalt antal samtidiga arbetare (begäranden)| 400 | 800 | 1600 | 3200 |6000 |
| Maximalt antal samtidiga sessioner |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium tjänstenivån

| **Beräkna storlek** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximala DTU:er | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Lagringsutrymme (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maximalt antal lagringsalternativ (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Maximal InMemory-OLTP-lagring (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximalt antal samtidiga arbetare (begäranden)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximalt antal samtidiga sessioner | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner förutom: Kina, östra; Kina, norra; Tyskland, centrala; Tyskland, nordöstra; USA, västra centrala; US DoD-regioner samt US Government Central. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Databas: Ändra lagringsstorlek

- DTU-priset för en enskild databas innehåller en viss mängd lagringsutrymme utan extra kostnad. Extra lagringsutrymme utöver mängden kan etableras för en ytterligare kostnad upp till den maximala storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB mer än 1 TB. Inkluderad lagring belopp och max storleksgränser finns i [enkel databas: Lagringsstorlekar och storlekar på](#single-database-storage-sizes-and-compute-sizes).
- Extra lagringsutrymme för en enskild databas kan etableras genom att öka sin maximala storlek med hjälp av den [Azure-portalen](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-storage-size), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), eller [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Priset för extra lagringsutrymme för en enskild databas är det extra lagringsutrymmet multiplicerat med extra lagringsutrymme enhetspriset för tjänstnivån. Mer information om priset för extra lagringsutrymme finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Databas: Ändra dtu: er

När du har valt en tjänstnivå och beräkningsstorleken lagringsutrymme, du kan skala en enskild databas upp eller ned dynamiskt utifrån det faktiska resultatet med hjälp av den [Azure-portalen](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [ PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), eller [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update).

I följande video visas dynamiskt ändra tjänsten nivå och beräkna storleken för att öka tillgängliga dtu: er för en enskild databas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Tjänsten ändrades till nivå och/eller beräkna storleken på en databas skapas en replik av den ursprungliga databasen till den nya beräkningsstorleken och sedan växlas anslutningar över till repliken. Inga data förloras under den här processen men under kort tidsperiod när vi växlar över till repliken är anslutningar till databasen inaktiverade, så vissa transaktioner som sker då kan återställas. Hur lång tid för switch-over varierar, men är mindre än 30 sekunder 99% av tiden. Om det finns stora mängder transaktioner som rör sig just då anslutningarna är inaktiverade, hur lång tid för switch-over kan också ta längre.

Hur lång tid processen att skala upp tar beror på databasens storlek och tjänstnivå före och efter ändringen. Till exempel bör en 250 GB-databas som ändras till, från eller inom en Standard-tjänstnivå slutföras inom sex timmar. För en databas med samma storlek som ändras instansstorlekarna i Premium-tjänstnivån, bör skala upp slutföras inom tre timmar.

> [!TIP]
> För att övervaka åtgärder som pågår, se: [Hantera åtgärder med hjälp av REST-API SQL](https://docs.microsoft.com/rest/api/sql/databaseoperations/listbydatabase
), [hantera åtgärder med hjälp av CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

- Om du uppgraderar till en högre tjänstnivå eller compute storlek ökar den maximala databasstorleken inte såvida du inte uttryckligen anger en större storlek (maxsize).
- Databasutrymme används måste vara mindre än det maximalt tillåtna storleken för måltjänstnivån och beräkningsstorleken för att nedgradera en databas.
- När du nedgraderar från **Premium** till den **Standard** nivå, en extra lagringskostnaderna gäller om både (1) den maximala storleken på databasen som stöds i Målstorlek för beräkning och (2) den maximala storleken överskrider den medföljande lagringsutrymme för mål compute storlek. Till exempel om en P1-databas med en maximal storlek på 500 GB downsized till S3, gäller en extra lagringskostnaderna eftersom S3 stöder en maximal storlek på 500 GB och dess lagringsutrymme är endast 250 GB. Så är det extra lagringsutrymmet 500 GB-250 GB = 250 GB. Priser för extra lagringsutrymme finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än det lagringsutrymme, och sedan detta extra kostnader kan undvikas genom att minska den maximala databasstorleken till mängden som ingår.
- När du uppgraderar en databas med [geo-replikering](sql-database-geo-replication-portal.md) aktiverat, uppgradera dess sekundära databaser på önskad tjänstnivån och beräkna storleken innan du uppgraderar den primära databasen (allmänna riktlinjer för bästa prestanda). När du uppgraderar till en annan måste krävs uppgradera den sekundära databasen först.
- När du nedgraderar en databas med [geo-replikering](sql-database-geo-replication-portal.md) aktiverat, nedgradera dess primära databaser på önskad tjänstnivån och beräkna storleken innan du nedgraderar den sekundära databasen (allmänna riktlinjer för bästa prestanda). När du nedgraderar till en annan utgåva krävs nedgradera den primära databasen först.
- Erbjudandena för återställningstjänsterna är olika för de olika tjänstnivåerna. Om du nedgraderar till den **grundläggande** nivå, finns det en lägre kvarhållningsperiod. Se [Azure SQL Database-säkerhetskopior](sql-database-automated-backups.md).
- De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Databas: Begränsningar för P11 och P15 när maximalt storleken är större än 1 TB

Följande överväganden och begränsningar gäller för P11 och P15-databaser med en maximal storlek som är större än 1 TB:

- Om du väljer en maximal storlek som är större än 1 TB när du skapar en databas (med ett värde på 4 TB eller 4096 GB), misslyckas kommandot för att skapa ett fel om databasen har etablerats i en region som stöds inte.
- För befintliga P11 och P15-databaser finns på någon av regionerna som stöds kan du öka det maximala lagringsutrymmet till mer än 1 TB i steg om 256 GB upp till 4 TB. Om du vill se om en större storlek stöds i din region, Använd den [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) fungera eller granska databasens storlek i Azure-portalen. Uppgradera en befintlig P11 eller P15 kan databasen endast utföras av en huvudsaklig inloggning på servernivå eller av medlemmar i rollen dbmanager.
- Om en uppgraderingsåtgärd körs i en region som stöds konfigurationen uppdateras direkt. Databasen förblir online under uppgraderingsprocessen. Du kan inte dock använda fullständig mängden lagringsutrymme till mer än 1 TB lagringsutrymme förrän faktiska databasfilerna har uppgraderats till den största storleken. Hur lång tid som krävs beror på storleken på databasen håller på att uppgraderas.
- När du skapar eller uppdaterar en P11 eller P15-databas, kan du bara välja mellan 1 TB och 4 TB i steg om 256 GB maximal storlek. När du skapar en P11/P15 är standardalternativet för lagring på 1 TB förvalda. Du kan öka storage max till upp till 4 TB för en ny eller befintlig databas för databaser som finns på någon av regionerna som stöds. Den maximala storleken kan inte utökas över 1 TB för alla andra regioner. Priset ändras inte när du väljer 4 TB lagringsutrymme.
- Om den maximala storleken för en databas är större än 1 TB, sedan den kan inte ändras till 1 TB även om den faktiska lagring som används är lägre än 1 TB. Så kan inte du nedgradera en P11 eller P15 med en maximal storlek som är större än 1 TB till en 1 TB P11 eller 1 TB P15 eller lägre compute storlek, till exempel P1 – P6). Den här begränsningen gäller även för återställning och kopiera scenarier inklusive point-in-time, geo-återställning, lång-sikt – backup-kvarhållning och databaskopian. När en databas har konfigurerats med en maximal storlek som är större än 1 TB, måste du köra alla återställningsåtgärder för den här databasen till en P11/P15 med en maximal storlek som är större än 1 TB.
- För scenarier med aktiv geo-replikering:
  - Konfigurera geo-replikeringsrelation: Om den primära databasen är P11 eller P15, måste secondary(ies) också vara P11 eller P15; lägre instansstorlekarna avvisas som sekundärservrar eftersom de inte kan stödja mer än 1 TB.
  - Uppgradera den primära databasen i en relation för geo-replikering: Ändra den maximala storleken till mer än 1 TB på en primär databas utlöser samma ändringar på den sekundära databasen. Båda uppgraderingar måste genomföras för att ändringen på primärt ska börja gälla. Region begränsningar för mer än 1 TB-alternativet. Om sekundärt finns i en region som inte stöder mer än 1 TB, uppgraderas inte primärt.
- Med tjänsten Import/Export för att läsa in P11/P15-databaser med mer än 1 TB stöds inte. Använda SqlPackage.exe till [importera](sql-database-import.md) och [exportera](sql-database-export.md) data.

## <a name="next-steps"></a>Nästa steg

- VCore-resursgränser för en enskild databas, se [resursgränser för enskilda databaser med hjälp av den vCore-baserade inköpsmodellen](sql-database-vcore-resource-limits-single-databases.md)
- VCore-resursgränser för elastiska pooler, se [resursgränser för elastiska pooler med hjälp av den vCore-baserade inköpsmodellen](sql-database-vcore-resource-limits-elastic-pools.md)
- DTU-resursgränser för elastiska pooler, se [resursgränser för elastiska pooler med hjälp av den DTU-baserade inköpsmodellen](sql-database-dtu-resource-limits-elastic-pools.md)
- Resursgränser för hanterade instanser, se [hanterad instans resursbegränsningar](sql-database-managed-instance-resource-limits.md).
- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Läs om hur resursbegränsningar på en databasserver, [översikt över resursgränser på en SQL-databasserver](sql-database-resource-limits-database-server.md) information om begränsningar på de server och prenumeration.