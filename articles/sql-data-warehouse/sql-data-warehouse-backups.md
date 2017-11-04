---
title: "Säkerhetskopieringar i Azure SQL Data Warehouse - ögonblicksbilder, geo-redundant | Microsoft Docs"
description: "Mer information om SQL Data Warehouse inbyggd databassäkerhetskopieringar som gör att du kan återställa en Azure SQL Data Warehouse till en återställningspunkt eller en annan geografisk region."
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: e76349ef7a2afa02d4f9e5295f299bb8084d1e08
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Säkerhetskopiering och återställning i SQL Data Warehouse
Den här artikeln beskriver egenskaperna för säkerhetskopiering i SQL Data Warehouse. Använda säkerhetskopiering av data warehouse för att återställa en databas ögonblicksbild till den primära regionen eller återställa en geo-säkerhetskopia till din geo länkas region. 

## <a name="what-is-a-data-warehouse-backup"></a>Vad är en säkerhetskopiering av data warehouse?
En säkerhetskopiering av data warehouse är kopia av databasen som du kan använda för att återställa ett datalager.  Eftersom SQL Data Warehouse är ett distribuerat system, består en data warehouse-säkerhetskopia av många filer som finns i Azure-lagring. En säkerhetskopiering av data warehouse innehåller både lokalt databasögonblicksbilder och geo-säkerhetskopior av alla databaser och filer som är associerade med ett datalager. 

## <a name="local-snapshot-backups"></a>Lokal ögonblicksbild säkerhetskopieringar
SQL Data Warehouse tar ögonblicksbilder av datalagret under dagen. Ögonblicksbilder är tillgängliga i sju dagar. SQL Data Warehouse stöder en åtta timmar återställningspunktmål (RPO). Du kan återställa ditt informationslager i den primära regionen av ögonblicksbilder som vidtagits under de senaste sju dagarna.

Om du vill se när den senaste ögonblicksbilden startas, kör du den här frågan på ditt online SQL Data Warehouse. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>GEO-säkerhetskopieringar
SQL Data Warehouse säkerhetskopierar geo-en gång per dag för att en [parad Datacenter](../best-practices-availability-paired-regions.md). Återställningspunktmålet för geo-återställning är 24 timmar. Du kan återställa geo-säkerhetskopiering till servern i området geo länkas. GEO-säkerhetskopiering gör att du kan återställa datalagret om du inte kommer åt ögonblicksbilder i din primära region.

GEO-säkerhetskopior är aktiverade som standard. Om ditt data warehouse är optimerad för elasticitet, kan du [avanmälas](https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicyredirectedfrom=msdn) om du vill. Du kan inte välja bort geo-säkerhetskopior med den optimerade för beräkning prestandanivå.

## <a name="backup-costs"></a>Kostnaderna för säkerhetskopiering
Du ser växeln Azure har en artikel för Azure Premium-lagring och ett radobjekt för geo-redundant lagring. Premium-lagring tillägget är den totala kostnaden för att lagra data i den primära region som innehåller ögonblicksbilder.  Geo-redundant tillägget omfattar kostnaden för att lagra geo-säkerhetskopior.  

Den totala kostnaden för ditt primära datalagret och sju dagar i Azure Blob-ögonblicksbilder avrundas till närmaste TB. Om ditt data warehouse är 1,5 TB och ögonblicksbilderna använder 100 GB, debiteras du till exempel för 2 TB data till Azure Premium Storage-priser. 

> [!NOTE]
> Varje ögonblicksbild är tom från början och växer när du gör ändringar i den primära datalagret. Alla ögonblicksbilder ökar i storlek när data warehouse ändras. Lagringskostnader för ögonblicksbilder växer därför enligt ändringshastigheten.
> 
> 

Om du använder geo-redundant lagring, får du en separat lagring kostnad. Geo-redundant lagring faktureras enligt standardkostnaden geografiskt Redundant lagring med läsbehörighet (RA-GRS).

Mer information om priser för SQL Data Warehouse finns [priser för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Ögonblicksbilden kvarhållning när ett informationslager har pausats
SQL Data Warehouse skapar inte ögonblicksbilder och upphör inte ögonblicksbilder medan ett informationslager har pausats. En ögonblicksbild ålder ändras inte när datalagret har pausats. Ögonblicksbild kvarhållning baseras på hur många dagar data warehouse är online, inte kalenderdagar.

Om en ögonblicksbild startas 1 oktober klockan 4 och datalagret har pausats 3 oktober klockan 4 är ögonblicksbilderna upp till två dagar. När datalagret är tillbaka online är ögonblicksbilden två dagar gammal. Om datalagret är online 5 oktober klockan 4 ögonblicksbilden är två dagar gammal och förblir i fem dagar.

När datalagret går tillbaka online SQL Data Warehouse återupptar nya ögonblicksbilder och upphör att gälla ögonblicksbilder när de har mer än sju dagar data.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>Kan jag återställa borttagna datalagret?
När du släpper ett data warehouse, SQL Data Warehouse skapar en slutlig ögonblicksbild och sparar den under sju dagar. Du kan återställa datalagret till den sista återställningspunkten som skapades på borttagning. 

> [!IMPORTANT]
> Om du tar bort en logisk SQL server-instansen tas även bort alla databaser som tillhör instansen och kan inte återställas. Du kan inte återställa en server som har tagits bort.
> 

## <a name="next-steps"></a>Nästa steg
Om du vill återställa en SQL data warehouse finns [återställer ett SQL data warehouse](sql-data-warehouse-restore-database-overview.md).

En översikt över verksamhetskontinuitet, se [översikt över verksamhetskontinuitet](../sql-database/sql-database-business-continuity.md)
