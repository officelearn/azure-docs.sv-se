---
title: "Säkerhetskopieringar i Azure SQL Data Warehouse - ögonblicksbilder, geo-redundant | Microsoft Docs"
description: "Mer information om SQL Data Warehouse inbyggd databassäkerhetskopieringar som gör att du kan återställa en Azure SQL Data Warehouse till en återställningspunkt eller en annan geografisk region."
services: sql-data-warehouse
documentationcenter: 
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: 54c0149a769e654139bbdf709802d49127f041ac
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="sql-data-warehouse-backups"></a>SQL Data Warehouse-säkerhetskopieringar
SQL Data Warehouse erbjuder både lokala och geografiska säkerhetskopior som en del av dess data warehouse-säkerhetskopiering. Dessa inkluderar Azure Storage Blob ögonblicksbilder och geo-redundant lagring. Använd data warehouse säkerhetskopieringar för att återställa ditt datalager till en återställningspunkt i den primära regionen eller Återställ till en annan geografisk region. Den här artikeln beskriver egenskaperna för säkerhetskopiering i SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>Vad är en säkerhetskopiering av data warehouse?
En säkerhetskopiering av data warehouse är data som du kan använda för att återställa ett datalager till en viss tid.  Eftersom SQL Data Warehouse är ett distribuerat system, består en data warehouse-säkerhetskopia av många filer som lagras i Azure BLOB. 

Säkerhetskopiorna av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Mer information finns i [översikt över verksamhetskontinuitet](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Dataredundans
SQL Data Warehouse skyddar dina data genom att lagra data i lokalt redundant (LRS) Azure Premium-lagring. Funktionen Azure Storage lagrar flera synkrona kopior av data i det lokala datacentret för att garantera transparent dataskydd om lokaliserade fel. Dataredundans garanterar att data kan överleva infrastruktur problem som till exempel diskfel. Dataredundans säkerställer kontinuitet för företag med en feltolerant och infrastruktur med hög tillgänglighet.

Mer information om:

* Azure Premium-lagring finns [introduktion till Azure Premium Storage](../storage/common/storage-premium-storage.md).
* Lokalt Redundant lagring finns [Azure Storage-replikering](../storage/common/storage-redundancy.md#locally-redundant-storage).

## <a name="azure-storage-blob-snapshots"></a>Azure Storage Blob-ögonblicksbilder
SQL Data Warehouse använder Azure Storage Blob ögonblicksbilder för att säkerhetskopiera data warehouse lokalt som en fördel med att använda Azure Premium-lagring. Du kan återställa ett datalager till en ögonblicksbild återställningspunkt. Ögonblicksbilder starta minst var åttonde timme och är tillgängliga i sju dagar.  

Mer information om:

* Azure blob-ögonblicksbilder finns [skapa en ögonblicksbild av blob](../storage/blobs/storage-blob-snapshots.md).

## <a name="geo-redundant-backups"></a>GEO-redundant säkerhetskopieringar
Var 24: e timme lagrar SQL Data Warehouse fullständig datalagret i standardlagring. Fullständig datalagret skapas så att den matchar tiden för den senaste ögonblicksbilden. Standardlagring hör till ett konto för geo-redundant lagring med läsbehörighet (RA-GRS). Funktionen Azure Storage RA-GRS replikeras de säkerhetskopiera filerna till en [parad Datacenter](../best-practices-availability-paired-regions.md). Den här georeplikering gör att du kan återställa datalagret om du inte kommer åt ögonblicksbilder i din primära region. 

Den här funktionen är aktiverad som standard. Om du inte vill använda geo-redundant säkerhetskopior du kan [avanmälas] (https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicy?redirectedfrom=msdn). 

> [!NOTE]
> I Azure storage termen *replikering* refererar till kopiera filer från en plats till en annan. SQL- *databasreplikering* refererar till att hålla till flera sekundära databaser som synkroniseras med en primär databas. 
> 
> 

> [!NOTE]
> Du kan inte välja bort geo-redundant säkerhetskopior med DWU 9000 och DWU 18000. 
>
> 

Mer information om:

* GEO-redundant lagring finns [Azure Storage-replikering](../storage/common/storage-redundancy.md).
* RA-GRS lagring finns [geo-redundant lagring med läsbehörighet](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Schemat för säkerhetskopiering och kvarhållningsperiod datalager
SQL Data Warehouse skapar ögonblicksbilder på dina online-datalager som alla fyra till åtta timmar och fortsätter varje ögonblicksbild i sju dagar. Du kan återställa databasen online till en återställningspunkter under de senaste sju dagarna. 

Om du vill se när den senaste ögonblicksbilden startas, kör du den här frågan på ditt online SQL Data Warehouse. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Om du vill behålla en ögonblicksbild för längre än sju dagar kan du återställa en återställningspunkt till ett nytt datalager. När återställningen är klar startar SQL Data Warehouse att skapa ögonblicksbilder på nytt datalager. Om du inte ändrar Nytt datalager ögonblicksbilderna förblir tom och därför ögonblicksbild kostnaden är minimal. Du kan också pausa databas om du vill behålla SQL Data Warehouse från att skapa ögonblicksbilder.

### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>Vad händer med min lagring av säkerhetskopior medan min datalagret pausas?
SQL Data Warehouse skapar inte ögonblicksbilder och upphör inte ögonblicksbilder medan ett informationslager har pausats. En ögonblicksbild ålder ändras inte när datalagret har pausats. Ögonblicksbild kvarhållning baseras på hur många dagar data warehouse är online, inte kalenderdagar.

Om en ögonblicksbild startas 1 oktober klockan 4 och datalagret har pausats 3 oktober klockan 4, är ögonblicksbilden två dagar gammal. När datalagret går tillbaka online är ögonblicksbilden två dagar gammal. Om datalagret är online 5 oktober klockan 4 ögonblicksbilden är två dagar gammal och förblir i fem dagar.

När datalagret går tillbaka online SQL Data Warehouse återupptar nya ögonblicksbilder och upphör att gälla ögonblicksbilder när de har mer än sju dagar data.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Hur lång tid är kvarhållningsperiod för en släppt datalagret?
När ett informationslager släpps, datalagret och ögonblicksbilderna sparas i sju dagar och sedan har tagits bort. Du kan återställa data warehouse till någon av de sparade återställningspunkterna.

> [!IMPORTANT]
> Om du tar bort en logisk SQL server-instansen tas även bort alla databaser som tillhör instansen och kan inte återställas. Du kan inte återställa en server som har tagits bort.
> 
> 

## <a name="data-warehouse-backup-costs"></a>Data warehouse säkerhetskopiering kostnader
Den totala kostnaden för ditt primära datalagret och sju dagar i Azure Blob-ögonblicksbilder avrundas till närmaste TB. Om ditt data warehouse är 1,5 TB och ögonblicksbilderna använder 100 GB, debiteras du till exempel för 2 TB data till Azure Premium Storage-priser. 

> [!NOTE]
> Varje ögonblicksbild är tom från början och växer när du gör ändringar i den primära datalagret. Alla ögonblicksbilder ökar i storlek när data warehouse ändras. Lagringskostnader för ögonblicksbilder växer därför enligt ändringshastigheten.
> 
> 

Om du använder geo-redundant lagring, får du en separat lagring kostnad. Geo-redundant lagring faktureras enligt standardkostnaden geografiskt Redundant lagring med läsbehörighet (RA-GRS).

Mer information om priser för SQL Data Warehouse finns [priser för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Med hjälp av databassäkerhetskopieringar
Primär användning för säkerhetskopiering av SQL data warehouse är att återställa datalagret till en återställningspunkter inom kvarhållningsperioden.  

* Om du vill återställa en SQL data warehouse finns [återställer ett SQL data warehouse](sql-data-warehouse-restore-database-overview.md).

## <a name="related-topics"></a>Relaterade ämnen
### <a name="scenarios"></a>Scenarier
* En översikt över verksamhetskontinuitet, se [översikt över verksamhetskontinuitet](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

* Om du vill återställa ett informationslager finns [återställer ett SQL data warehouse](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

