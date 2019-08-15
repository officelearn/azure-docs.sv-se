---
title: Azure SQL Data Warehouse säkerhets kopiering och återställning – ögonblicks bilder, Geo-redundant | Microsoft Docs
description: Lär dig hur säkerhets kopiering och återställning fungerar i Azure SQL Data Warehouse. Använd säkerhets kopieringar för data lager för att återställa data lagret till en återställnings punkt i den primära regionen. Använd geo-redundanta säkerhets kopieringar för att återställa till en annan geografisk region.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/30/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 90544e182eb25f53232cee9a4dd0c05bd25508a3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988479"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Säkerhets kopiering och återställning i Azure SQL Data Warehouse

Lär dig hur du använder säkerhets kopiering och återställning i Azure SQL Data Warehouse. Använd återställnings punkter för data lager för att återställa eller kopiera data lagret till ett tidigare tillstånd i den primära regionen. Använd informations lager geo-redundanta säkerhets kopieringar för att återställa till en annan geografisk region.

## <a name="what-is-a-data-warehouse-snapshot"></a>Vad är en ögonblicks bild av informations lagret

En *ögonblicks bild av ett informations lager* skapar en återställnings punkt som du kan använda för att återställa eller kopiera data lagret till ett tidigare tillstånd.  Eftersom SQL Data Warehouse är ett distribuerat system består en ögonblicks bild av ett informations lager av många filer som finns i Azure Storage. Ögonblicks bilder fångar stegvisa ändringar från data som lagras i ditt informations lager.

En *data lager återställning* är ett nytt informations lager som skapas från en återställnings punkt för ett befintligt eller Borttaget data lager. Att återställa ditt informations lager är en viktig del av en affärs kontinuitet och katastrof återställnings strategi eftersom den återskapar dina data efter haveri skada eller borttagning. Data lagret är också en kraftfull mekanism för att skapa kopior av ditt informations lager i test-eller utvecklings syfte.  SQL Data Warehouse återställnings priser kan variera beroende på databasens storlek och platsen för käll-och mål data lagret. I genomsnitt inom samma region tar återställnings frekvensen vanligt vis cirka 20 minuter. 

## <a name="automatic-restore-points"></a>Automatiska återställningspunkter

Ögonblicks bilder är en inbyggd funktion i tjänsten som skapar återställnings punkter. Du behöver inte aktivera den här funktionen. Det går för närvarande inte att ta bort automatiska återställnings punkter av användare där tjänsten använder dessa återställnings punkter för att underhålla service avtal för återställning.

SQL Data Warehouse tar ögonblicks bilder av ditt informations lager under dagen som skapar återställnings punkter som är tillgängliga i sju dagar. Kvarhållningsperioden kan inte ändras. SQL Data Warehouse stöder en 8-timmars återställnings punkt mål. Du kan återställa ditt informations lager i den primära regionen från någon av de ögonblicks bilder som tagits under de senaste sju dagarna.

För att se när den senaste ögonblicks bilden startade, kör du den här frågan på din SQL Data Warehouse online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Användardefinierade återställningspunkter

Med den här funktionen kan du utlösa ögonblicks bilder manuellt för att skapa återställnings punkter för ditt informations lager innan och efter stora ändringar. Den här funktionen säkerställer att återställnings punkter logiskt konsekvent, vilket ger ytterligare data skydd i händelse av arbets belastnings avbrott eller användar fel för snabb återställnings tid. Användardefinierade återställnings punkter är tillgängliga i sju dagar och tas automatiskt bort för din räkning. Det går inte att ändra kvarhållningsperioden för användardefinierade återställnings punkter. **42 användardefinierade återställnings punkter** garanteras vid varje tidpunkt så att de måste [tas bort](https://go.microsoft.com/fwlink/?linkid=875299) innan en annan återställnings punkt skapas. Du kan utlösa ögonblicks bilder för att skapa användardefinierade återställnings punkter via [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) eller Azure Portal.

> [!NOTE]
> Om du behöver återställnings punkter längre än 7 dagar bör du rösta på den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Du kan också skapa en användardefinierad återställnings punkt och återställa från den nyligen skapade återställnings punkten till ett nytt data lager. När du har återställt har du data lagret online och kan pausa det oändligt för att spara beräknings kostnader. Den pausade databasen ådrar sig lagrings kostnader med Azure Premium Storage rate. Om du behöver en aktiv kopia av det återställda informations lagret kan du återuppta vilket som bör ta några minuter.

### <a name="restore-point-retention"></a>Kvarhållning av återställnings punkt

Följande visar information om återställnings punkter för återställnings punkter:

1. SQL Data Warehouse tar bort en återställnings punkt när den får en kvarhållningsperiod på 7 dagar **och** när det finns minst 42 sammanlagt återställnings punkter (inklusive både användardefinierad och automatisk)
2. Ögonblicks bilder tas inte när ett informations lager pausas
3. En återställnings punkts ålder mäts av de absoluta kalender dagarna från den tidpunkt då återställnings punkten tas, inklusive när data lagret har pausats
4. Vid en viss tidpunkt är ett informations lager säkert att kunna lagra upp till 42 användardefinierade återställnings punkter och 42 automatiska återställnings punkter så länge dessa återställnings punkter inte har uppnått den sju dagars kvarhållningsperioden
5. Om en ögonblicks bild tas pausas data lagret i mer än 7 dagar, och sedan återupptas, det är möjligt att återställnings punkten är kvar tills det finns 42 totala återställnings punkter (inklusive både användardefinierad och automatisk)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Kvarhållning av ögonblicks bilder när ett informations lager släpps

När du släpper ett informations lager skapar SQL Data Warehouse en slutgiltig ögonblicks bild och sparar den i sju dagar. Du kan återställa data lagret till den sista återställnings punkten som skapas vid borttagning.

> [!IMPORTANT]
> Om du tar bort en logisk SQL Server-instans raderas även alla databaser som tillhör instansen och de kan inte återställas. Det går inte att återställa en borttagen Server.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-säkerhets kopiering och haveri beredskap

SQL Data Warehouse utför en geo-säkerhetskopiering en gång per dag till ett [parat Data Center](../best-practices-availability-paired-regions.md). Återställningen för en geo-återställning är 24 timmar. Du kan återställa geo-säkerhetskopiering till en server i en annan region där SQL Data Warehouse stöds. En geo-säkerhetskopiering garanterar att du kan återställa data lagret om du inte kan komma åt återställnings punkterna i din primära region.

Geo-säkerhets kopieringar är aktiverat som standard. Om ditt informations lager är gen1 kan du [välja](/powershell/module/az.sql/set-azsqldatabasegeobackuppolicy) om du vill. Det går inte att välja geo-säkerhetskopieringar för Gen2 eftersom data skydd är en inbyggd garanterad garanti.

> [!NOTE]
> Om du behöver en kortare återställnings punkt för geo-säkerhetskopiering, röst för den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse). Du kan också skapa en användardefinierad återställnings punkt och återställa från den nyligen skapade återställnings punkten till ett nytt informations lager i en annan region. När du har återställt har du data lagret online och kan pausa det oändligt för att spara beräknings kostnader. Den pausade databasen ådrar sig lagrings kostnader med Azure Premium Storage rate. Om du behöver en aktiv kopia av data lagret kan du fortsätta vilket som bör ta några minuter.

## <a name="backup-and-restore-costs"></a>Säkerhetskopiera och återställa kostnader

Observera att Azure-fakturan har ett rad objekt för lagring och ett rad objekt för haveri beredskaps lagring. Lagrings avgiften är den totala kostnaden för att lagra dina data i den primära regionen tillsammans med de stegvisa ändringar som fångas av ögonblicks bilder. En mer detaljerad förklaring av hur ögonblicks bilder debiteras finns i [förstå hur ögonblicks](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)bilder debiteras. Den geo-redundanta avgiften täcker kostnaden för lagring av Geo-säkerhetskopieringar.  

Den totala kostnaden för ditt primära informations lager och sju dagars ögonblicks bilds ändringar avrundas till närmaste TB. Om ditt informations lager till exempel är 1,5 TB och ögonblicks bilderna fångar 100 GB debiteras du för 2 TB data med Azure Premium Storage priser.

Om du använder Geo-redundant lagring får du en separat lagrings avgift. Geo-redundant lagring debiteras enligt standard priset för Läs åtkomst till geografiskt redundant lagring (RA-GRS).

Mer information om SQL Data Warehouse priser finns i [SQL Data Warehouse prissättning]. Du debiteras inte för utgående data vid återställning mellan regioner.

## <a name="restoring-from-restore-points"></a>Återställa från återställnings punkter

Varje ögonblicks bild skapar en återställnings punkt som representerar tidpunkten då ögonblicks bilden startades. Om du vill återställa ett informations lager väljer du en återställnings punkt och utfärdar kommandot Restore.  

Du kan antingen behålla det återställda informations lagret och det aktuella, eller ta bort ett av dem. Om du vill ersätta det aktuella data lagret med det återställda informations lagret kan du byta namn på det med hjälp av [Alter Database (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) med alternativet ändra namn.

Om du vill återställa ett informations lager, se [återställa ett informations lager med hjälp av Azure Portal](sql-data-warehouse-restore-database-portal.md), [återställa ett informations lager med PowerShell](sql-data-warehouse-restore-database-powershell.md)eller [återställa ett informations lager med hjälp av REST API: er](sql-data-warehouse-restore-database-rest-api.md).

Om du vill återställa ett borttaget eller pausat data lager kan du [skapa ett support ärende](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Återställning mellan prenumerationer

Om du behöver återställa direkt mellan prenumerationen kan du rösta på den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Återställ till en annan logisk server och [Flytta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) servern över prenumerationer för att utföra en återställning mellan prenumerationer. 

## <a name="geo-redundant-restore"></a>Geo-redundant återställning

Du kan [återställa ditt informations lager](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup#restore-from-an-azure-geographical-region-through-powershell) till en region som stöder SQL Data Warehouse på din valda prestanda nivå.

> [!NOTE]
> Om du vill utföra en Geo-redundant återställning får du inte välja att använda den här funktionen.

## <a name="next-steps"></a>Nästa steg

Mer information om haveri planering finns i [Översikt över affärs kontinuitet](../sql-database/sql-database-business-continuity.md)
