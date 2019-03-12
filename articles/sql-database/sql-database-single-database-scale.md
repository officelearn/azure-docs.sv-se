---
title: Skala resurser för enkel databas – Azure SQL Database | Microsoft Docs
description: Den här artikeln beskriver hur du skalar beräknings- och lagringsresurser som är tillgängliga för en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: d6de6e2752c16d95c81f47b9d14d53e0233a4ed6
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570807"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skala resurser för enkel databas i Azure SQL Database

Den här artikeln beskriver hur du skalar beräknings- och lagringsresurser som är tillgängliga för en enskild databas i Azure SQL Database.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="change-compute-resources-vcores-or-dtus"></a>Ändra beräkningsresurser (virtuella kärnor eller dtu: er)

När du har valt antalet virtuella kärnor eller dtu: er, du kan skala en enskild databas upp eller ned dynamiskt utifrån det faktiska resultatet med hjälp av den [Azure-portalen](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [ PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), eller [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update).

I följande video visas dynamiskt ändra tjänsten nivå och beräkna storleken för att öka tillgängliga dtu: er för en enskild databas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Effekten av att ändra tjänst-nivå eller skalas om beräkningsstorleken

Tjänsten ändrades till nivå eller beräkna storleken på en enskild databas främst innebär att tjänsten utföra följande steg:

1. Skapa ny beräkningsinstans för databasen  

    En ny beräkningsinstans för databasen skapas med den begärda tjänstnivå och beräkningsstorleken. För vissa kombinationer av tjänstnivå och beräkning storleksändringar en replik av databasen måste skapas i den nya compute-instansen som innebär att kopiera data och starkt kan påverka den totala svarstiden. Oavsett databasen förblir online under det här steget och anslutningar fortsätter så att de dirigeras till databasen i den ursprungliga beräkningsinstansen.

2. Växla routning av anslutningar till nya beräkningsinstans

    Befintliga anslutningar till databasen i den ursprungliga beräkningsinstansen ignoreras. Alla nya anslutningar upprättas till databasen i den nya compute-instansen. För vissa kombinationer av tjänstnivå och beräkning storleksändringar databasfiler är oberoende och återansluta under växeln.  Oavsett kan växeln resultera i ett kort avbrott när databasen är otillgänglig Allmänt i mindre än 30 sekunder och ofta bara några sekunder. Om det finns tidskrävande transaktioner som körs när anslutningar släpps, varaktigheten för det här steget kan ta längre tid för att återställa avbrutna transaktioner. [Accelererad databasåterställning](sql-database-accelerated-database-recovery.md) kan minska påverkan på avbryts tidskrävande transaktioner.

> [!IMPORTANT]
> Inga data förloras under något steg i arbetsflödet.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Svarstiden för att ändra tjänst-nivå eller skalas om beräkningsstorleken

Fördröjning för att ändra tjänstnivå eller skala om beräkningsstorleken för en enkel databas eller elastisk pool är parameteriserat på följande sätt:

|Tjänstenivå|Grundläggande enkel databas</br>Standard (S0-S1)|Grundläggande elastisk pool</br>Standard (S2-S12), </br>I hyperskala </br>Allmänt syfte enkel databas eller elastisk pool|Premium- eller affärskritiska enkel databas eller elastisk pool|
|:---|:---|:---|:---|
|**Grundläggande enkel databas,</br> Standard (S0-S1)**|&bull; &nbsp;Konstant tidsfördröjningen som är oberoende av använt utrymme</br>&bull; &nbsp;Vanligtvis, mindre än 5 minuter|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|
|**Grundläggande elastisk pool, </br>Standard (S2-S12) </br>i hyperskala </br>generell användning enkel databas eller elastisk pool**|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|&bull; &nbsp;Konstant tidsfördröjningen som är oberoende av använt utrymme</br>&bull; &nbsp;Vanligtvis, mindre än 5 minuter|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|
|**Premium- eller affärskritiska enkel databas eller elastisk pool**|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|

> [!TIP]
> För att övervaka åtgärder som pågår, se: [Hantera åtgärder med hjälp av REST-API SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [hantera åtgärder med hjälp av CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Ytterligare överväganden när du ändrar tjänsten nivå eller skalas om beräkningsstorleken

- Om du uppgraderar till en högre tjänstnivå eller compute storlek ökar den maximala databasstorleken inte såvida du inte uttryckligen anger en större storlek (maxsize).
- Databasutrymme används måste vara mindre än det maximalt tillåtna storleken för måltjänstnivån och beräkningsstorleken för att nedgradera en databas.
- När du nedgraderar från **Premium** till den **Standard** nivå, en extra lagringskostnaderna gäller om både (1) den maximala storleken på databasen som stöds i Målstorlek för beräkning och (2) den maximala storleken överskrider den medföljande lagringsutrymme för mål compute storlek. Till exempel om en P1-databas med en maximal storlek på 500 GB downsized till S3, gäller en extra lagringskostnaderna eftersom S3 stöder en maximal storlek på 500 GB och dess lagringsutrymme är endast 250 GB. Så är det extra lagringsutrymmet 500 GB-250 GB = 250 GB. Priser för extra lagringsutrymme finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än det lagringsutrymme, och sedan detta extra kostnader kan undvikas genom att minska den maximala databasstorleken till mängden som ingår.
- När du uppgraderar en databas med [geo-replikering](sql-database-geo-replication-portal.md) aktiverat, uppgradera dess sekundära databaser på önskad tjänstnivån och beräkna storleken innan du uppgraderar den primära databasen (allmänna riktlinjer för bästa prestanda). När du uppgraderar till en annan måste krävs uppgradera den sekundära databasen först.
- När du nedgraderar en databas med [geo-replikering](sql-database-geo-replication-portal.md) aktiverat, nedgradera dess primära databaser på önskad tjänstnivån och beräkna storleken innan du nedgraderar den sekundära databasen (allmänna riktlinjer för bästa prestanda). När du nedgraderar till en annan utgåva krävs nedgradera den primära databasen först.
- Erbjudandena för återställningstjänsterna är olika för de olika tjänstnivåerna. Om du nedgraderar till den **grundläggande** nivå, finns det en lägre kvarhållningsperiod. Se [Azure SQL Database-säkerhetskopior](sql-database-automated-backups.md).
- De nya egenskaperna för databasen tillämpas inte förrän ändringarna har slutförts.

### <a name="billing-during-rescaling"></a>Fakturering under skalas om

Du debiteras för varje timme som det finns en databas med hjälp av den högsta tjänstenivå + compute storlek som gällde under den timmen, oavsett användning eller om databasen var aktiv under mindre än en timme. Om du skapar en enkel databas och raderar den fem minuter senare visar din faktura en avgift för en databastimme.

## <a name="change-storage-size"></a>Ändra lagringsstorlek

### <a name="vcore-based-purchasing-model"></a>Virtuell kärna-baserad inköpsmodell

- Storage kan etableras upp till den maximala storleksgränsen med hjälp av steg om 1 GB. Den minsta konfigurerbara datalagringen är 5 GB
- Lagring för en enskild databas kan etableras genom att öka eller minska sin maximala storlek med hjälp av den [Azure-portalen](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), eller [REST-API](https://docs.microsoft.com/rest/api/sql/databases/update).
- SQL Database tilldelar automatiskt 30% av ytterligare lagringsutrymme för loggfilerna och 32GB per vCore för TempDB, men inte överskrida 384GB. TempDB finns på en ansluten SSD på alla tjänstnivåer.
- Priset för lagringen för en enskild databas är summan av data lagring och log storage multiplicerat med a-pris för lagring av tjänstnivån. Kostnaden för TempDB ingår i det vCore-priset. Mer information om priset för extra lagringsutrymme finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodellen

- DTU-priset för en enskild databas innehåller en viss mängd lagringsutrymme utan extra kostnad. Extra lagringsutrymme utöver mängden kan etableras för en ytterligare kostnad upp till den maximala storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB mer än 1 TB. Inkluderad lagring belopp och max storleksgränser finns i [enkel databas: Lagringsstorlekar och storlekar på](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Extra lagringsutrymme för en enskild databas kan etableras genom att öka sin maximala storlek med hjälp av Azure-portalen [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), eller [ REST-API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Priset för extra lagringsutrymme för en enskild databas är det extra lagringsutrymmet multiplicerat med extra lagringsutrymme enhetspriset för tjänstnivån. Mer information om priset för extra lagringsutrymme finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>DTU-baserade inköpsmodellen: Begränsningar för P11 och P15 när maximalt storleken är större än 1 TB

Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner förutom: Kina, östra; Kina, norra; Tyskland, centrala; Tyskland, nordöstra; USA, västra centrala; US DoD-regioner samt US Government Central. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB. Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-single-database-scale.md#dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb). Följande överväganden och begränsningar gäller för P11 och P15-databaser med en maximal storlek som är större än 1 TB:

- Om du väljer en maximal storlek som är större än 1 TB när du skapar en databas (med ett värde på 4 TB eller 4096 GB), misslyckas kommandot för att skapa ett fel om databasen har etablerats i en region som stöds inte.
- För befintliga P11 och P15-databaser finns på någon av regionerna som stöds kan du öka det maximala lagringsutrymmet till mer än 1 TB i steg om 256 GB upp till 4 TB. Om du vill se om en större storlek stöds i din region, Använd den [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) fungera eller granska databasens storlek i Azure-portalen. Uppgradera en befintlig P11 eller P15 kan databasen endast utföras av en huvudsaklig inloggning på servernivå eller av medlemmar i rollen dbmanager.
- Om en uppgraderingsåtgärd körs i en region som stöds konfigurationen uppdateras direkt. Databasen förblir online under uppgraderingsprocessen. Du kan inte dock använda fullständig mängden lagringsutrymme till mer än 1 TB lagringsutrymme förrän faktiska databasfilerna har uppgraderats till den största storleken. Hur lång tid som krävs beror på storleken på databasen håller på att uppgraderas.
- När du skapar eller uppdaterar en P11 eller P15-databas, kan du bara välja mellan 1 TB och 4 TB i steg om 256 GB maximal storlek. När du skapar en P11/P15 är standardalternativet för lagring på 1 TB förvalda. Du kan öka storage max till upp till 4 TB för en ny eller befintlig databas för databaser som finns på någon av regionerna som stöds. Den maximala storleken kan inte utökas över 1 TB för alla andra regioner. Priset ändras inte när du väljer 4 TB lagringsutrymme.
- Om den maximala storleken för en databas är större än 1 TB, sedan den kan inte ändras till 1 TB även om den faktiska lagring som används är lägre än 1 TB. Så kan inte du nedgradera en P11 eller P15 med en maximal storlek som är större än 1 TB till en 1 TB P11 eller 1 TB P15 eller lägre compute storlek, till exempel P1 – P6). Den här begränsningen gäller även för återställning och kopiera scenarier inklusive point-in-time, geo-återställning, lång-sikt – backup-kvarhållning och databaskopian. När en databas har konfigurerats med en maximal storlek som är större än 1 TB, måste du köra alla återställningsåtgärder för den här databasen till en P11/P15 med en maximal storlek som är större än 1 TB.
- För scenarier med aktiv geo-replikering:
  - Konfigurera geo-replikeringsrelation: Om den primära databasen är P11 eller P15, måste secondary(ies) också vara P11 eller P15; lägre beräkningsstorleken avvisas som sekundärservrar eftersom de inte kan stödja mer än 1 TB.
  - Uppgradera den primära databasen i en relation för geo-replikering: Ändra den maximala storleken till mer än 1 TB på en primär databas utlöser samma ändringar på den sekundära databasen. Båda uppgraderingar måste genomföras för att ändringen på primärt ska börja gälla. Region begränsningar för mer än 1 TB-alternativet. Om sekundärt finns i en region som inte stöder mer än 1 TB, uppgraderas inte primärt.
- Med tjänsten Import/Export för att läsa in P11/P15-databaser med mer än 1 TB stöds inte. Använda SqlPackage.exe till [importera](sql-database-import.md) och [exportera](sql-database-export.md) data.

## <a name="next-steps"></a>Nästa steg

Övergripande resursbegränsningar finns [SQL Database vCore-baserade resursbegränsningar - enskilda databaser](sql-database-vcore-resource-limits-single-databases.md) och [SQL Database DTU-baserade resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-single-databases.md).
