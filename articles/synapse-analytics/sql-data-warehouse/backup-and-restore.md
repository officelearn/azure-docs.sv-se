---
title: Säkerhetskopiering och återställning - ögonblicksbilder, geouppsagda
description: Lär dig hur säkerhetskopiering och återställning fungerar i Azure Synapse Analytics SQL-pool. Använd säkerhetskopior för att återställa informationslagret till en återställningspunkt i den primära regionen. Använd geo-redundanta säkerhetskopior för att återställa till ett annat geografiskt område.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 1d82c7c22bb5aeb2740884b0d7ede4a4d8f07f86
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631220"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Säkerhetskopiera och återställa i Azure Synapse SQL-pool

Lär dig hur du använder säkerhetskopiering och återställning i Azure Synapse SQL-pool. Använd SQL-poolåterställningspunkter för att återställa eller kopiera informationslagret till ett tidigare tillstånd i den primära regionen. Använd geoundundundundanta säkerhetskopior för datalager för att återställa till ett annat geografiskt område.

## <a name="what-is-a-data-warehouse-snapshot"></a>Vad är en ögonblicksbild av informationslager

En *ögonblicksbild av informationslager* skapar en återställningspunkt som du kan utnyttja för att återställa eller kopiera ditt informationslager till ett tidigare tillstånd.  Eftersom SQL-pool är ett distribuerat system består en ögonblicksbild av informationslager av många filer som finns i Azure-lagring. Ögonblicksbilder samlar inkrementella ändringar från data som lagras i ditt informationslager.

En återställning av *informationslager* är ett nytt informationslager som skapas från en återställningspunkt för ett befintligt eller borttaget informationslager. Att återställa ditt informationslager är en viktig del av alla affärskontinuiteter och katastrofåterställningsstrategier eftersom det återskapar dina data efter oavsiktlig skada eller borttagning. Informationslager är också en kraftfull mekanism för att skapa kopior av ditt informationslager för test- eller utvecklingsändamål.  Sql pool återställningshastigheter kan variera beroende på databasens storlek och plats för källan och måldatalagret.

## <a name="automatic-restore-points"></a>Automatiska återställningspunkter

Ögonblicksbilder är en inbyggd funktion i tjänsten som skapar återställningspunkter. Du behöver inte aktivera den här funktionen. SQL-poolen bör dock vara i ett aktivt tillstånd för att skapa återställningspunkt. Om SQL-poolen pausas ofta kan det hända att automatiska återställningspunkter inte skapas, så se till att skapa användardefinierad återställningspunkt innan SQL-poolen pausas. Automatiska återställningspunkter kan för närvarande inte tas bort av användare eftersom tjänsten använder dessa återställningspunkter för att underhålla SLA:er för återställning.

Ögonblicksbilder av ditt informationslager tas under hela dagen skapa återställningspunkter som är tillgängliga i sju dagar. Det går inte att ändra kvarhållningsperioden. SQL-poolen stöder ett åtta timmars återställningspunktsmål (RPO). Du kan återställa ditt informationslager i den primära regionen från någon av de ögonblicksbilder som tagits under de senaste sju dagarna.

Om du vill se när den senaste ögonblicksbilden startade kör du den här frågan på din SQL-pool online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Användardefinierade återställningspunkter

Med den här funktionen kan du manuellt utlösa ögonblicksbilder för att skapa återställningspunkter i informationslagret före och efter stora ändringar. Den här funktionen säkerställer att återställningspunkterna är logiskt konsekventa, vilket ger ytterligare dataskydd vid avbrott i arbetsbelastningen eller användarfel för snabb återställningstid. Användardefinierade återställningspunkter är tillgängliga i sju dagar och raderas automatiskt för din räkning. Du kan inte ändra kvarhållningsperioden för användardefinierade återställningspunkter. **42 användardefinierade återställningspunkter** garanteras när som helst så att de måste [tas bort](https://go.microsoft.com/fwlink/?linkid=875299) innan de skapar en annan återställningspunkt. Du kan utlösa ögonblicksbilder för att skapa användardefinierade återställningspunkter via [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) eller Azure-portalen.

> [!NOTE]
> Om du behöver återställa poäng längre än 7 dagar, vänligen rösta för denna funktion [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Du kan också skapa en användardefinierad återställningspunkt och återställa från den nyligen skapade återställningspunkten till ett nytt informationslager. När du har återställt har du SQL-poolen online och kan pausa den på obestämd tid för att spara beräkningskostnader. Den pausade databasen medför lagringsavgifter till Azure Premium Storage-priset. Om du behöver en aktiv kopia av det återställda informationslagret kan du återuppta vilket bör ta bara några minuter.

### <a name="restore-point-retention"></a>Kvarhållning av återställningspunkt

I följande lista visas information om kvarhållandeperioder för återställningspunkt:

1. SQL-poolen tar bort en återställningspunkt när den når 7-dagars kvarhållningsperioden **och** när det finns minst 42 totala återställningspunkter (inklusive både användardefinierade och automatiska).
2. Ögonblicksbilder tas inte när en SQL-pool pausas.
3. Åldern på en återställningspunkt mäts med de absoluta kalenderdagarna från den tidpunkt då återställningspunkten tas, inklusive när SQL-poolen pausas.
4. Vid varje tidpunkt är en SQL-pool garanterad att kunna lagra upp till 42 användardefinierade återställningspunkter och 42 automatiska återställningspunkter så länge dessa återställningspunkter inte har nått 7-dagars kvarhållningsperioden
5. Om en ögonblicksbild tas pausas SQL-poolen sedan i mer än 7 dagar och återupptas sedan, återställningspunkten kvarstår tills det finns 42 totala återställningspunkter (inklusive både användardefinierade och automatiska)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Ögonblicksbildkvarhållning när en SQL-pool tas bort

När du släpper en SQL-pool skapas och sparas en slutlig ögonblicksbild i sju dagar. Du kan återställa SQL-poolen till den slutliga återställningspunkten som skapats vid borttagning. Om SQL-poolen tas i pausat tillstånd tas ingen ögonblicksbild. I det scenariot, se till att skapa en användardefinierad återställningspunkt innan du släpper SQL-poolen.

> [!IMPORTANT]
> Om du tar bort en logisk SQL-serverinstans tas även alla databaser som tillhör instansen bort och kan inte återställas. Du kan inte återställa en borttagen server.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-säkerhetskopior och haveriberedskap

En geo-säkerhetskopiering skapas en gång per dag till ett [parkopplat datacenter](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). RPO för en geo-återställning är 24 timmar. Du kan återställa geo-säkerhetskopieringen till en server i alla andra regioner där SQL-pool stöds. En geo-säkerhetskopiering säkerställer att du kan återställa informationslager om du inte kan komma åt återställningspunkterna i din primära region.

> [!NOTE]
> Om du behöver en kortare RPO för geo-säkerhetskopior, rösta för den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse). Du kan också skapa en användardefinierad återställningspunkt och återställa från den nyligen skapade återställningspunkten till ett nytt informationslager i en annan region. När du har återställt har du informationslagret online och kan pausa det på obestämd tid för att spara beräkningskostnader. Den pausade databasen medför lagringsavgifter till Azure Premium Storage-priset. Om du behöver en aktiv kopia av informationslagret kan du återuppta vilket bör ta bara några minuter.

## <a name="backup-and-restore-costs"></a>Kostnader för säkerhetskopiering och återställning

Du kommer att märka att Azure-fakturan har en radartikel för lagring och en radartikel för lagring av haveriberedskap. Lagringsavgiften är den totala kostnaden för att lagra dina data i den primära regionen tillsammans med de inkrementella ändringarna som fångas upp av ögonblicksbilder. En mer detaljerad förklaring av hur ögonblicksbilder debiteras finns [i Förstå hur ögonblicksbilder samlar in avgifter](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Den geoundundanta avgiften täcker kostnaden för lagring av geo-säkerhetskopior.  

Den totala kostnaden för ditt primära informationslager och sju dagar med ögonblicksbildändringar avrundas till närmaste TB. Om ditt informationslager till exempel är 1,5 TB och ögonblicksbilderna samlar in 100 GB debiteras du för 2 TB data till Azure Premium Storage-priser.

Om du använder geoupptundret lagringsutrymme får du en separat lagringsavgift. Den geo-redundanta lagringen faktureras enligt ra-GRS-standardhastigheten för geografiskt redundant lagring (Read-Access Geografiskt redundant lagring).

Mer information om Azure Synapse-priser finns i [Azure Synapse-priser](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Du debiteras inte för data som går ut när du återställer mellan regioner.

## <a name="restoring-from-restore-points"></a>Återställa från återställningspunkter

Varje ögonblicksbild skapar en återställningspunkt som representerar den tid ögonblicksbilden startade. Om du vill återställa ett informationslager väljer du en återställningspunkt och utfärdar ett återställningskommando.  

Du kan antingen behålla det återställda informationslagret och det aktuella, eller ta bort ett av dem. Om du vill ersätta det aktuella informationslagret med det återställda informationslagret kan du byta namn på det med [alter database (SQL-pool)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) med alternativet ÄNDRA NAMN.

Information om hur du återställer ett informationslager finns i [Återställa en SQL-pool](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Om du vill återställa ett borttaget eller pausat informationslager kan du [skapa en supportbiljett](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Återställning av flera prenumerationer

Om du behöver återställa prenumerationen direkt kan du rösta på den här [funktionen](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Återställ till en annan logisk server och [Flytta](/azure/azure-resource-manager/resource-group-move-resources?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) servern över prenumerationer för att utföra en återställning mellan prenumerationer.

## <a name="geo-redundant-restore"></a>Geo-redundant återställning

Du kan [återställa SQL-poolen](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) till alla regioner som stöder SQL-pool på din valda prestandanivå.

> [!NOTE]
> Om du vill utföra en geoundundant återställning får du inte ha valt bort den här funktionen.

## <a name="next-steps"></a>Nästa steg

Mer information om katastrofplanering finns i [Översikt över affärskontinuitet](../../sql-database/sql-database-business-continuity.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
