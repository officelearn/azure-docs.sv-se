---
title: Azure SQL Data Warehouse-säkerhetskopiering och återställning – ögonblicksbilder, geo-redundant | Microsoft Docs
description: Lär dig hur säkerhetskopiering och återställning fungerar i Azure SQL Data Warehouse. Använd säkerhetskopior av informationslager att återställa ditt informationslager till en återställningspunkt i den primära regionen. Återställ till en annan geografisk region med hjälp av geo-redundanta säkerhetskopieringar.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/30/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 914513bc19cc81da29efef12d50a6485233d169f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236569"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Säkerhetskopiering och återställning i Azure SQL Data Warehouse

Lär dig hur du använder säkerhetskopiering och återställning i Azure SQL Data Warehouse. Använda återställningspunkter för data warehouse för att återställa eller kopiera ditt informationslager till ett tidigare tillstånd i den primära regionen. Användningsdata warehouse geo-redundanta säkerhetskopieringar att återställa till en annan geografisk region.

## <a name="what-is-a-data-warehouse-snapshot"></a>Vad är en ögonblicksbild av data warehouse

En *data warehouse ögonblicksbild* skapar en återställningspunkt som du kan använda för att återställa eller kopiera din datalager till ett tidigare tillstånd.  Eftersom SQL Data Warehouse är ett distribuerat system, består en ögonblicksbild av data warehouse med många filer som finns i Azure storage. Sparar hårdiskdata inkrementella ändringar från data som lagras i ditt informationslager.

En *datalager återställning* är ett nytt datalager som har skapats från en återställningspunkt för en befintlig eller borttagna data warehouse. Återställa ditt informationslager är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom den återskapar dina data efter oavsiktliga skador eller tas bort. Data warehouse är också en kraftfull mekanism för att skapa kopior av ditt informationslager för test- eller utvecklingsmiljö.  SQL Data Warehouse återställning priser kan variera beroende på databasens storlek och plats för källa och mål-datalagret. I genomsnitt inom samma region ta återställning priserna vanligtvis cirka 20 minuter. 

## <a name="automatic-restore-points"></a>Automatiska återställningspunkter

Ögonblicksbilder är en inbyggd funktion i tjänsten som skapar återställningspunkter. Du behöver inte aktivera den här funktionen. Automatiska återställningspunkter för närvarande kan inte tas bort av användare där tjänsten använder dessa återställningspunkter pekar Underhåll serviceavtal för återställning.

SQL Data Warehouse tar ögonblicksbilder av informationslagret under dagen skapar återställningspunkter som är tillgängliga i sju dagar. Den här kvarhållningsperioden kan inte ändras. SQL Data Warehouse stöder en snabbutvärdering på åtta timmar mål för återställningspunkt (RPO). Du kan återställa ditt informationslager i den primära regionen från någon av ögonblicksbilder som tas under de senaste sju dagarna.

Om du vill se när den senaste ögonblicksbilden startas, kör du den här frågan på online SQL Data Warehouse.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Användardefinierade återställningspunkter

Den här funktionen kan du manuellt utlösaren ögonblicksbilder att skapa återställningspunkter för ditt informationslager före och efter stora ändringar. Den här funktionen ser till att återställningspunkter är logiskt konsekvent, som tillhandahåller ytterligare dataskydd vid eventuella avbrott i arbetsbelastningen och användarfel för tiden för snabb återställning. Användardefinierade återställningspunkter är tillgängliga i sju dagar och tas bort automatiskt åt dig. Du kan inte ändra kvarhållningsperioden för användardefinierade återställningspunkter. **återställningspunkter för 42 användardefinierade** garanterat när som helst i tid så att de måste vara [bort](https://go.microsoft.com/fwlink/?linkid=875299) innan du skapar en annan återställningspunkt. Du kan utlösa ögonblicksbilder för att skapa en användardefinierad återställningspunkter via [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) eller Azure-portalen.

> [!NOTE]
> Om du behöver återställningspunkter som är längre än 7 dagar kan du rösta på den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Du kan också skapa en återställningspunkt för användardefinierade och återställa från den nyligen skapade återställningspunkten till ett nytt datalager. När du har återställt ha datalagret online och kan pausa på obestämd tid för att spara beräkningskostnader. Pausad databasen medför avgifter för lagring till Azure Premium Storage-kostnad. Om du behöver en aktiv kopia av återställda data warehouse kan återuppta du som tar bara några minuter.

### <a name="restore-point-retention"></a>Återställa kvarhållning av återställningspunkt

Information om återställningspunkt kvarhållningsperioder följande listor:

1. SQL Data Warehouse tar bort en återställningspunkt när den når 7 dagars kvarhållningsperioden **och** när det finns minst 42 Totalt antal återställningspunkter (inklusive både en användardefinierad och automatisk)
2. Ögonblicksbilder tas inte när ett informationslager har pausats
3. Ålder för en återställningspunkt mäts absolut kalenderdagar från tidpunkten då återställningspunkten tas även när informationslagret har pausats
4. Vid varje givet tillfälle, ett informationslager är korrekt för att kunna lagra upp till 42 användardefinierade återställningspunkter och 42 Automatiska-återställningspunkter är så länge återställningspunkterna inte har nått kvarhållningsperiod för 7 dagar
5. Om en ögonblicksbild tas, datalagret sedan har pausats för fler än 7 dagar och återupptar, är det möjligt för återställningspunkt för att bevara tills det inte finns 42 Totalt antal återställningspunkter (inklusive både en användardefinierad och automatisk)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Ögonblicksbild kvarhållning när ett informationslager har släppts

När du släpper ett data warehouse, SQL Data Warehouse skapar en slutlig ögonblicksbild och sparar den i sju dagar. Du kan återställa datalagret till den sista återställningspunkten som skapas vid borttagning.

> [!IMPORTANT]
> Om du tar bort en logisk SQL server-instans, raderas också alla databaser som tillhör instansen och kan inte återställas. Du kan inte återställa en borttagen server.

## <a name="geo-backups-and-disaster-recovery"></a>GEO-säkerhetskopiering och disaster recovery

SQL Data Warehouse utför en geo-säkerhetskopia en gång per dag för att en [kopplat Datacenter](../best-practices-availability-paired-regions.md). Återställningspunktmålet för en geo-återställning är 24 timmar. Du kan återställa geo-säkerhetskopiering till en server i alla andra regioner där det finns stöd för SQL Data Warehouse. En geo-säkerhetskopia säkerställer att du kan återställa datalagret om du inte åtkomst till återställningspunkter i den primära regionen.

GEO-säkerhetskopiering är aktiverat som standard. Om ditt informationslager är Gen1, kan du [avanmäla dig](/powershell/module/az.sql/set-azsqldatabasegeobackuppolicy) om du vill. Du kan inte välja bort geo-säkerhetskopiering för Gen2 eftersom dataskydd är en inbyggd garanteras.

> [!NOTE]
> Om du behöver ett kortare Återställningspunktmål för geo-säkerhetskopiering kan rösta på den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse). Du kan också skapa en återställningspunkt för användardefinierade och återställa från den nyligen skapade återställningspunkten till ett nytt datalager i en annan region. När du har återställt ha datalagret online och kan pausa på obestämd tid för att spara beräkningskostnader. Pausad databasen medför avgifter för lagring till Azure Premium Storage-kostnad. Om du behöver en aktiv kopia av datalagret, kan du återuppta som tar bara några minuter.

## <a name="backup-and-restore-costs"></a>Kostnader för säkerhetskopiering och återställning

Du ser Azure-faktura har ett radobjekt för lagring och ett radobjekt för Disaster Recovery-lagring. Avgiften för lagring är den totala kostnaden för att lagra data i den primära regionen tillsammans med de inkrementella ändringar som avbildas av ögonblicksbilder. En mer detaljerad förklaring av hur ögonblicksbilder debiteras avser [förstå hur ögonblicksbilder påförs avgifter](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). Geo-redundant avgiften täcker kostnaden för lagring av geo-säkerhetskopiering.  

Den totala kostnaden för din primära datalagret och sju dagar efter ögonblicksbild ändringar avrundas till närmaste TB. Till exempel att om ditt informationslager är 1,5 TB och ögonblicksbilderna samlar in 100 GB, kommer du att debiteras för 2 TB data enligt priserna för Azure Premium Storage.

Om du använder geo-redundant lagring, får du en separat lagringsfaktura. Geo-redundant lagring debiteras enligt standardavgifterna för läsåtkomst till geografiskt Redundant lagring (RA-GRS).

Mer information om priser för SQL Data Warehouse finns i [SQL Data Warehouse priser]. Du debiteras inte för utgående data när du återställer i olika regioner.

## <a name="restoring-from-restore-points"></a>Återställa från återställningspunkter

Varje ögonblicksbild skapar en återställningspunkt som representerar den tid som ögonblicksbilden igång. Om du vill återställa ett informationslager, Välj en återställningspunkt och utfärda ett kommando för återställning.  

Du kan antingen behålla de återställda data warehouse och det aktuella eller ta bort en av dem. Om du vill ersätta det aktuella datalagret med återställda data warehouse, du kan byta namn på den med hjälp av [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) med alternativet Ändra namnet.

Om du vill återställa ett informationslager, se [Återställ ett informationslager med Azure portal](sql-data-warehouse-restore-database-portal.md), [Återställ ett informationslager med hjälp av PowerShell](sql-data-warehouse-restore-database-powershell.md), eller [Återställ ett informationslager med hjälp av REST API: er](sql-data-warehouse-restore-database-rest-api.md).

Om du vill återställa en borttagen eller pausat informationslager kan du [skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Mellan återställning

Om du vill återställa direkt över prenumerationer kan rösta på den här funktionen [här](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Återställ till en annan logisk server och ['Move-](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) servern mellan prenumerationer att utföra en återställning av över prenumerationer. 

## <a name="geo-redundant-restore"></a>GEO-redundant återställning

Du kan [återställa ditt informationslager](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) till valfri region som stöd för SQL Data Warehouse på din valda prestandanivå.

> [!NOTE]
> Om du vill utföra en geo-redundant återställning måste du inte har valt ut från den här funktionen.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du planerar för haveriberedskap, [översikt över affärskontinuitet](../sql-database/sql-database-business-continuity.md)
