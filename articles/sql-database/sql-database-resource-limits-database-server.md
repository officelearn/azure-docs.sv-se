---
title: Azure SQL Database resurs gränser | Microsoft Docs
description: Den här artikeln ger en översikt över Azure SQL Database resurs gränser för enskilda databaser och elastiska pooler. Den innehåller också information om vad som händer när dessa resurs gränser nåtts eller överskrids.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: fa41649e002bd4845b95e787c1d0589ed1987588
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587251"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL Database resurs gränser och resurs styrning

Den här artikeln innehåller en översikt över SQL Database resurs gränser för en SQL Database Server som hanterar enskilda databaser och elastiska pooler. Den innehåller information om vad som händer när dessa resurs gränser nåtts eller överskrids och beskriver de resurs styrnings mekanismer som används för att genomdriva dessa gränser.

> [!NOTE]
> Begränsningar för hanterade instanser finns i [SQL Database resurs gränser för hanterade instanser](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Högsta antal resurs gränser

| Resurs | Gräns |
| :--- | :--- |
| Databaser per server | 5000 |
| Standard antal servrar per prenumeration i valfri region | 20 |
| Maximalt antal servrar per prenumeration i valfri region | 200 |  
| Kvot för DTU/eDTU per server | 54,000 |  
| vCore-kvot per Server/instans | 540 |
| Högsta antal pooler per server | Begränsas av antalet DTU: er eller virtuella kärnor. Om varje pool till exempel är 1000 DTU: er, kan en server stödja 54-pooler.|
|||

> [!IMPORTANT]
> När antalet databaser närmar sig gränsen per SQL Database Server kan följande inträffa:
>
> - Ökande svars tid för att köra frågor mot huvud databasen.  Detta inkluderar vyer av statistik över resursutnyttjande, till exempel sys. resource_stats.
> - Ökande svars tid i hanterings åtgärder och åter givning av Portal synvinklar som innefattar att räkna upp databaser på servern.

> [!NOTE]
> För att få mer DTU/eDTU-kvot, vCore kvot eller fler servrar än standard beloppet skickar du en ny supportbegäran i Azure Portal. Mer information finns i [begäran om kvot ökning för Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Lagrings storlek

För resurs lagrings storlekar för enskilda databaser, referera till antingen [DTU-baserade resurs gränser](sql-database-dtu-resource-limits-single-databases.md) eller [vCore resurs gränser](sql-database-vcore-resource-limits-single-databases.md) för lagrings storleks gränser per pris nivå.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Vad händer när databas resurs gränser nås

### <a name="compute-dtus-and-edtus--vcores"></a>Compute (DTU: er och eDTU: er/virtuella kärnor)

När databas beräknings användningen (uppmätt av DTU: er och eDTU: er, eller virtuella kärnor) blir hög, ökar svars tiden för frågor och frågor kan till och med ta lång tid. Under dessa villkor kan frågor placeras i kö av tjänsten och de tillhandahålls resurser för att köras när resurser blir kostnads fria.
När du räknar med hög beräknings användning är följande alternativ för minskning:

- Öka beräknings storleken för databasen eller den elastiska poolen för att tillhandahålla databasen med fler beräknings resurser. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastiska pooler](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursutnyttjande för varje fråga. Mer information finns i [fråga om justering/tips](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

När databas utrymmet som används når den maximala storleks gränsen, infogas och uppdateras databasen som ökar data storleken och klienterna får ett [fel meddelande](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). SELECT-och DELETE-instruktioner fortsätter att fungera.

När du ska räkna med hög användnings utrymme är alternativen för minskning:

- Öka den maximala storleken på databasen eller den elastiska poolen eller lägga till mer lagrings utrymme. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastiska pooler](sql-database-elastic-pool-scale.md).
- Om databasen finns i en elastisk pool kan du eventuellt flytta databasen utanför poolen så att lagrings utrymmet inte delas med andra databaser.
- Krymp en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera fil utrymme i Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioner och arbetare (begär Anden)

Det maximala antalet sessioner och arbets tagare bestäms av tjänst nivån och beräknings storleken (DTU: er/eDTU: er eller virtuella kärnor. Nya begär Anden avvisas när sessioner eller arbets gränser nås och klienter får ett fel meddelande. Även om antalet tillgängliga anslutningar kan styras av programmet är antalet samtidiga arbetare ofta svårare att uppskatta och kontrol lera. Detta gäller särskilt under belastnings perioder när databas resurs gränser har nåtts och arbets grupper registreras på grund av längre körnings frågor, stora spärrnings kedjor eller alltför lång frågans parallellitet.

När du räknar med hög arbets belastning eller arbets belastning, är alternativ för minskning följande:

- Öka tjänst nivån eller beräknings storleken för databasen eller den elastiska poolen. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastiska pooler](sql-database-elastic-pool-scale.md).
- Optimering av frågor för att minska resursutnyttjande för varje fråga om orsaken till ökad arbets belastning beror på konkurrens för beräknings resurser. Mer information finns i [fråga om justering/tips](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="resource-governance"></a>Resursstyrning

Om du vill framtvinga resurs gränserna använder Azure SQL Database en resurs styrnings implementering som baseras på SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), modifierad och utökad för att köra en SQL Server databas tjänst i Azure. På varje SQL Server instans i tjänsten finns det flera [resurspooler](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) och [arbets belastnings grupper](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group), med resurs gränser inställda på både pool-och grupp nivåer för att tillhandahålla en [bal anse rad databas som en tjänst](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Användarens arbets belastning och interna arbets belastningar klassificeras till separata resurspooler och arbets belastnings grupper. Användarens arbets belastning på de primära och läsbara sekundära replikerna, inklusive geo-replikeringar, klassificeras i `SloSharedPool1` resurspoolen och `UserPrimaryGroup.DBId[N]` arbets belastnings gruppen där `N` står för databas-ID-värdet. Det finns dessutom flera resurspooler och arbets belastnings grupper för olika interna arbets belastningar.

Förutom att använda Resource Governor för att styra resurser inom SQL Servers processen, använder Azure SQL Database även Windows- [jobb objekt](https://docs.microsoft.com/windows/win32/procthread/job-objects) för resurs styrning på processnivå och Windows [hanteraren för fil server resurser (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) för lagrings kvot hantering.

Azure SQL Database resurs styrning är hierarkiskt beslagen. Uppifrån och ned tillämpas gränser på OS-nivå och på lagrings volym nivå med hjälp av mekanismer för styrning av operativ system resurser och Resource Governor, sedan på resurspoolen med Resource Governor och sedan på arbets belastnings grupps nivå med Resource Governor. Resurs styrnings gränser som gäller för den aktuella databasen eller den elastiska poolen visas i vyn [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . 

### <a name="data-io-governance"></a>Data IO-styrning

Data IO-styrning är en process i Azure SQL Database som används för att begränsa både Läs-och skriv fysisk IO mot datafiler i en databas. IOPS-gränser ställs in för varje service nivå för att minimera effekterna "störnings Grans kraft", för att tillhandahålla skälighet i tjänsten för flera innehavare och för att hålla sig inom funktionerna i den underliggande maskin varan och lagringen.

För enskilda databaser tillämpas gränser för arbets belastnings grupper för alla lagrings-i/o mot databasen, medan gränserna för resurspooler gäller för all lagrings-i/o för alla databaser på samma SQL Server instans, inklusive `tempdb`-databasen. För elastiska pooler gäller begränsningarna för arbets belastnings gruppen för varje databas i poolen, medan gränsen för resurspooler gäller hela den elastiska poolen, inklusive `tempdb` databasen, som delas mellan alla databaser i poolen. I allmänhet är det inte säkert att gränserna för resurspooler kan uppnås av arbets belastningen mot en databas (antingen en eller en pool), eftersom gränserna för arbets belastnings gruppen är lägre än begränsningen för resurspool och begränsar IOPS/genomflöde tidigare. Pool gränser kan dock nås av den kombinerade arbets belastningen mot flera databaser på samma SQL Server instans.

Om en fråga till exempel genererar 1000 IOPS utan någon IO-resurs styrning, men den högsta IOPS-gränsen för arbets belastnings gruppen är inställd på 900 IOPS, kan inte frågan generera mer än 900 IOPS. Men om max antalet IOPS för resurspoolen är inställt på 1500 IOPS och total i/o från alla arbets belastnings grupper som är associerade med resurspoolen överskrider 1500 IOPS, kan i/o för samma fråga minskas under arbets gruppens gräns på 900 IOPS.

Värdena för IOPS och data flöde som returnerades av [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) -vyn fungerar som gränser/versaler, inte som garantier. Vidare garanterar resurs styrning inte någon bestämd lagrings fördröjning. Den bästa tillgängliga svars tiden, IOPS och data flödet för en specifik användar arbets belastning är beroende inte bara av gränser för i/o-resursens styrning, utan även på den kombination av IO-storlek som används och på funktionerna i det underliggande lagrings utrymmet. SQL Server använder IOs som varierar i storlek mellan 512 KB och 4 MB. I syfte att framtvinga IOPS-gränser redovisas varje IO oavsett storlek, med undantag för databaser med datafiler i Azure Storage. I så fall redovisas IOs som är större än 256 KB som flera 256 KB IOs, för att justera med Azure Storage i/o-redovisning.

För Basic-, standard-och Generell användning-databaser, som använder datafiler i Azure Storage, kanske `primary_group_max_io` svärdet inte kan nås om en databas inte har tillräckligt med datafiler för att ackumulera antalet IOPS, eller om data inte fördelas jämnt över filer, eller om prestanda nivån för underliggande blobbar begränsar IOPS/data flödet under resurs styrnings gränsen. På samma sätt kan `primary_max_log_rate`-värdet inte uppnås av en arbets belastning på grund av IOPS-gränsen för den underliggande Azure Storage-blobben, med en liten logg-IOs som genereras av transaktions överföringen ofta.

Värdena för resursutnyttjande, till exempel `avg_data_io_percent` och `avg_log_write_percent`, som rapporteras i vyerna [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)och [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) beräknas som procent andelar av maximala resurs styrnings gränser. När andra faktorer än resurs styrningen begränsar IOPS/data flödet, är det möjligt att se att utökningar av IOPS/genom strömning och fördröjning ökar när arbets belastningen ökar, även om rapporterat resursutnyttjande är lägre än 100%. 

Om du vill se läsa och skriva IOPS, data flöde och svars tid per databas fil använder du funktionen [sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Den här funktionen delar alla IO: t i databasen, inklusive Background IO som inte redovisas mot `avg_data_io_percent`, men använder IOPS och data flödet för den underliggande lagringen och kan påverka observerad lagrings fördröjning. Funktionen hämtar också ytterligare latens som kan introduceras av i/o-resursens styrning för läsningar och skrivningar, i `io_stall_queued_read_ms` respektive `io_stall_queued_write_ms` kolumner.

### <a name="transaction-log-rate-governance"></a>Hastighets styrning för transaktions logg

Styrning av transaktions logg hastighet är en process i Azure SQL Database som används för att begränsa hög förbruknings frekvens för arbets belastningar som Mass infogning, SELECT INTO och indexe build. Dessa gränser spåras och framtvingas på den andra nivån till frekvensen för genereringen av logg poster, vilket begränsar data flödet, oavsett hur många IOs som kan utfärdas mot datafiler.  Taxan för transaktions logg skapande skalas linjärt upp till en punkt som är maskin vara beroende av, med den högsta logg frekvensen som tillåts som 96 MB/s med vCore inköps modell. 

> [!NOTE]
> Faktiska fysiska IOs till transaktionsloggfiler är inte reglerade eller begränsade.

Logg taxan ställs in så att de kan uppnås och hanteras i flera olika scenarier, medan det övergripande systemet kan underhålla sin funktionalitet med minimerad påverkan på användar belastningen. Styrning av logg hastighet säkerställer att säkerhets kopior av transaktions loggar stannar inom publicerings service avtal.  Denna styrning förhindrar också en alltför lång efter släpning på sekundära repliker.

När logg poster skapas utvärderas och utvärderas varje åtgärd för om den ska fördröjas för att upprätthålla den högsta önskade logg frekvensen (MB/s per sekund). Fördröjningarna läggs inte till när logg posterna töms på lagringen, i takt med att logg takts styrningen används vid själva genereringen av logg hastighet.

De faktiska taxan för logg skapande som påförs vid körning kan också påverkas av feedback-mekanismer, vilket tillfälligt minskar de tillåtna logg priserna så att systemet kan stabiliseras. Hantering av logg fil utrymme, Undvik att köra i slut på logg utrymmes villkor och replikering av tillgänglighets grupper kan tillfälligt minska de totala system gränserna.

Trafikstyrningen för logg hastighets styrning sker via följande vänte typer (visas i [sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Wait-typ | Anteckningar |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Databas begränsning |
| POOL_LOG_RATE_GOVERNOR | Begränsning av pool |
| INSTANCE_LOG_RATE_GOVERNOR | Begränsning på instans nivå |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedback-kontroll, fysisk replikering för tillgänglighets grupp i Premium/Affärskritisk inte hålla sig uppdaterad |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedback-kontroll, begränsa priser för att undvika ett slut på logg utrymmes villkor |
|||

När du påträffar en logg hastighets gräns som hindrar önskad skalbarhet, bör du överväga följande alternativ:
- Skala upp till en högre service nivå för att få maximal logg hastighet på 96 MB/s. 
- Om data som läses in är tillfälliga, till exempel mellanlagring av data i en ETL-process, kan den läsas in i tempdb (som är minimalt loggad). 
- För analys scenarier läser du in i en klustrad columnstore-tabell. Detta minskar den nödvändiga logg frekvensen på grund av komprimering. Den här tekniken ökar processor användningen och gäller endast för data uppsättningar som drar nytta av klustrade columnstore-index. 

## <a name="next-steps"></a>Nästa steg

- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om DTU: er och eDTU: er finns i [DTU: er och eDTU: er](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Information om tempdb-storleks gränser finns [i tempdb i Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
