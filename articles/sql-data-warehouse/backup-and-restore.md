---
title: Azure SQL Data Warehouse säkerhetskopiering och återställning - ögonblicksbilder, geo-redundant | Microsoft Docs
description: Lär dig hur säkerhetskopiering och återställning fungerar i Azure SQL Data Warehouse. Använd data warehouse säkerhetskopior som ska återställas ditt data warehouse till en återställningspunkt i den primära regionen. Använd geo-redundant säkerhetskopieringar för att återställa till en annan geografisk region.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a4f24aad95f13315eaeac790c9006ca00f61af69
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187607"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Säkerhetskopiering och återställning i Azure SQL Data Warehouse
Lär dig hur säkerhetskopiering och återställning fungerar i Azure SQL Data Warehouse. Använd data warehouse säkerhetskopior som ska återställas ditt data warehouse till en återställningspunkt i den primära regionen. Använd geo-redundant säkerhetskopieringar för att återställa till en annan geografisk region. 

## <a name="what-is-backup-and-restore"></a>Vad är säkerhetskopiering och återställning?
En *säkerhetskopiering av data warehouse* är en kopia av databasen som du kan använda för att återställa ett datalager.  Eftersom SQL Data Warehouse är ett distribuerat system, består en data warehouse-säkerhetskopia av många filer som finns i Azure-lagring. En säkerhetskopiering av data warehouse innehåller både lokalt databasögonblicksbilder och geo-säkerhetskopior av alla databaser och filer som är associerade med ett datalager. 

En *datalager återställning* är ett nytt datalager som har skapats från en säkerhetskopia av en befintlig eller borttagna data warehouse. Återställda data warehouse återskapar säkerhetskopierade data warehouse vid en viss tid. Du återställer data warehouse är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom den återskapar dina data efter oavsiktliga skador eller tas bort.

Både lokala och geografiska återställningar är en del av SQL Data Warehouse funktioner för katastrofåterställning. 

## <a name="local-snapshot-backups"></a>Lokal ögonblicksbild säkerhetskopieringar
Lokal ögonblicksbild säkerhetskopior är en inbyggd funktion för tjänsten.  Du behöver inte aktivera dem. 

SQL Data Warehouse tar ögonblicksbilder av datalagret under dagen. Ögonblicksbilder är tillgängliga i sju dagar. SQL Data Warehouse stöder en åtta timmar återställningspunktmål (RPO). Du kan återställa ditt informationslager i den primära regionen av ögonblicksbilder som vidtagits under de senaste sju dagarna.

Om du vill se när den senaste ögonblicksbilden startas, kör du den här frågan på ditt online SQL Data Warehouse. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Ögonblicksbilden kvarhållning när ett informationslager har pausats
SQL Data Warehouse skapar inte ögonblicksbilder och upphör inte ögonblicksbilder medan ett informationslager har pausats. En ögonblicksbild ålder ändras inte när datalagret har pausats. Ögonblicksbild kvarhållning baseras på hur många dagar data warehouse är online, inte kalenderdagar.

Om en ögonblicksbild startas 1 oktober klockan 4 och datalagret har pausats 3 oktober klockan 4 är ögonblicksbilderna upp till två dagar. När datalagret är tillbaka online är ögonblicksbilden två dagar gammal. Om datalagret är online 5 oktober klockan 4 ögonblicksbilden är två dagar gammal och förblir i fem dagar.

När datalagret går tillbaka online SQL Data Warehouse återupptar nya ögonblicksbilder och upphör att gälla ögonblicksbilder när de har mer än sju dagar data.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Ögonblicksbilden kvarhållning när ett informationslager har släppts
När du släpper ett data warehouse, SQL Data Warehouse skapar en slutlig ögonblicksbild och sparar den under sju dagar. Du kan återställa datalagret till den sista återställningspunkten som skapades på borttagning. 

> [!IMPORTANT]
> Om du tar bort en logisk SQL server-instansen tas även bort alla databaser som tillhör instansen och kan inte återställas. Du kan inte återställa en server som har tagits bort.
> 

## <a name="geo-backups"></a>GEO-säkerhetskopieringar
SQL Data Warehouse säkerhetskopierar geo-en gång per dag för att en [parad Datacenter](../best-practices-availability-paired-regions.md). Återställningspunktmålet för geo-återställning är 24 timmar. Du kan återställa geo-säkerhetskopiering till en server i en annan region där det finns stöd för SQL Data Warehouse. Geo-säkerhetskopiering gör att du kan återställa datalagret om du inte kommer åt ögonblicksbilder i din primära region.

GEO-säkerhetskopior är aktiverade som standard. Om ditt data warehouse är Gen1, kan du [avanmälas](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) om du vill. Du kan inte välja bort geo säkerhetskopieringar för Gen2 dataskydd är en inbyggd gurantee.

## <a name="backup-costs"></a>Kostnaderna för säkerhetskopiering
Du ser växeln Azure har en artikel för Azure Premium-lagring och ett radobjekt för geo-redundant lagring. Premium-lagring tillägget är den totala kostnaden för att lagra data i den primära region som innehåller ögonblicksbilder.  Geo-redundant tillägget omfattar kostnaden för att lagra geo-säkerhetskopior.  

Den totala kostnaden för ditt primära datalagret och sju dagar i Azure Blob-ögonblicksbilder avrundas till närmaste TB. Om ditt data warehouse är 1,5 TB och ögonblicksbilderna använder 100 GB, debiteras du till exempel för 2 TB data till Azure Premium Storage-priser. 

> [!NOTE]
> Varje ögonblicksbild är tom från början och växer när du gör ändringar i den primära datalagret. Alla ögonblicksbilder ökar i storlek när data warehouse ändras. Lagringskostnader för ögonblicksbilder växer därför enligt ändringshastigheten.
> 
> 

Om du använder geo-redundant lagring, får du en separat lagring kostnad. Geo-redundant lagring faktureras enligt standardkostnaden geografiskt Redundant lagring med läsbehörighet (RA-GRS).

Mer information om priser för SQL Data Warehouse finns [priser för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restoring-from-restore-points"></a>Återställa från återställningspunkter
Varje ögonblicksbild har en återställningspunkt som representerar den tid som ögonblicksbilden igång. Om du vill återställa ett datalager, Välj en återställningspunkt och utfärda ett kommando för återställning.  

SQL Data Warehouse återställs alltid säkerhetskopian till ett nytt datalager. Du kan hålla återställda data warehouse och det aktuella eller ta bort en av dem. Om du vill ersätta det aktuella datalagret med återställda data warehouse, du kan byta namn på den med hjälp av [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) med alternativet Ändra namnet. 

Om du vill återställa ett datalager, se [återställa ett data warehouse med Azure-portalen](sql-data-warehouse-restore-database-portal.md), [återställa ett data warehouse med hjälp av PowerShell](sql-data-warehouse-restore-database-powershell.md), eller [återställa ett data warehouse med hjälp av T-SQL](sql-data-warehouse-restore-database-rest-api.md) .

Om du vill återställa en borttagen eller pausas datalagret kan du [skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>GEO-redundant återställning
Du kan återställa ditt datalager till en region som stöder Azure SQL Data Warehouse på din valda prestandanivå. 

> [!NOTE]
> Om du vill utföra en återställning av geo-redundant måste du inte har valt utanför den här funktionen.
> 
> 

## <a name="restore-timeline"></a>Återställa tidslinjen
Du kan återställa en databas till vilken tillgänglig återställningspunkt under de senaste sju dagarna. Ögonblicksbilder starta alla fyra till åtta timmar och är tillgängliga i sju dagar. När en ögonblicksbild är äldre än sju dagar, den upphör att gälla och dess återställningspunkten är inte längre tillgänglig. 

Säkerhetskopieringar inte göras mot ett pausat data warehouse. Om ditt data warehouse är pausad för mer än sju dagar har du inte några återställningspunkter. 

## <a name="restore-costs"></a>Återställa kostnader
Storage-tillägget för återställda data warehouse faktureras till Azure Premium Storage-sats. 

Om du pausar ett återställda data warehouse, debiteras du för lagring till Azure Premium Storage-sats. Fördelen med att pausa är inte debiteras du för dataresurser.

Mer information om priser för SQL Data Warehouse finns [priser för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restore-use-cases"></a>Återställa användningsområden
Den primära data warehouse återställning används för att återställa data efter oavsiktlig dataförlust eller skadade data. Du kan också använda data warehouse återställning för att behålla en säkerhetskopia för längre än sju dagar. När säkerhetskopieringen har återställts har datalagret online och kan pausa under obestämd tid för att spara kostnader för beräkning. Pausad databas debiteras lagring till Azure Premium Storage-sats. 

## <a name="next-steps"></a>Nästa steg
Mer information om planering för katastrofåterställning finns [översikt över verksamhetskontinuitet](../sql-database/sql-database-business-continuity.md)
