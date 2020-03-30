---
title: Resursbegränsningar för Azure SQL Database | Microsoft-dokument
description: Den här artikeln innehåller en översikt över azure SQL Database-resursgränserna för enskilda databaser och elastiska pooler. Den ger också information om vad som händer när dessa resursgränser träffas eller överskrids.
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
ms.openlocfilehash: 550c315023c0ae907c369778c81b16e137004bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067249"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Resursbegränsningar och resursstyrning i SQL Database

Den här artikeln innehåller en översikt över SQL Database-resursgränserna för en SQL Database-server som hanterar enskilda databaser och elastiska pooler. Den ger information om vad som händer när dessa resursgränser träffas eller överskrids, och beskriver de resursstyrningsmekanismer som används för att tillämpa dessa gränser.

> [!NOTE]
> För begränsningar för hanterad instans, se [SQL Database-resursgränser för hanterade instanser](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximala resursgränser

| Resurs | Gräns |
| :--- | :--- |
| Databaser per server | 5000 |
| Standardantal servrar per prenumeration i valfri region | 20 |
| Max antal servrar per prenumeration i en region | 200 |  
| DTU / eDTU kvot per server | 54,000 |  
| vCore-kvot per server/instans | 540 |
| Max pooler per server | Begränsas av antalet DTUs eller virtuella kärnor. Om varje pool till exempel är 1 000 DU:er kan en server stödja 54 pooler.|
|||

> [!IMPORTANT]
> När antalet databaser närmar sig gränsen per SQL Database-server kan följande inträffa:
>
> - Öka svarstiden när du kör frågor mot huvuddatabasen.  Detta inkluderar vyer av resursanvändningsstatistik, till exempel sys.resource_stats.
> - Ökad svarstid i hanteringsåtgärder och renderingsportalsynpunkter som innebär att databaserumrar på servern.

> [!NOTE]
> Om du vill få mer DTU/eDTU-kvot, vCore-kvot eller fler servrar än standardbeloppet skickar du en ny supportbegäran i Azure-portalen. Mer information finns i [Begär kvotökningar för Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Lagringsstorlek

För enskilda databaser resurslagringsstorlekar, se antingen [DTU-baserade resursgränser](sql-database-dtu-resource-limits-single-databases.md) eller [vCore-baserade resursgränser](sql-database-vcore-resource-limits-single-databases.md) för lagringsstorleksgränserna per prisnivå.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Vad händer när begränsningar för databasresursen nås

### <a name="compute-dtus-and-edtus--vcores"></a>Beräkning (DTI och eDTUs / vCores)

När databasberäkningsanvändningen (mätt med DTU:er och eDTU:er eller virtuella kärnor) blir hög ökar frågefördröjningen och frågor kan till och med time out. Under dessa förhållanden kan frågor köas av tjänsten och tillhandahålls resurser för körning när resurser blir gratis.
När du stöter på hög beräkningsutnyttjande omfattar begränsningsalternativ:

- Öka beräkningsstorleken för databasen eller den elastiska poolen för att ge databasen fler beräkningsresurser. Se [Skala enskilda databasresurser](sql-database-single-database-scale.md) och [Skala elastiska poolresurser](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursutnyttjandet av varje fråga. Mer information finns i [Frågejustering/tips .](sql-database-performance-guidance.md#query-tuning-and-hinting)

### <a name="storage"></a>Lagring

När databasutrymmet som används når maxstorleksgränsen, infogar och uppdaterar databasen som ökar datastorleken misslyckas och klienter får ett [felmeddelande](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). SELECT- och DELETE-satser fortsätter att lyckas.

När du stöter på hög utrymmesutnyttjande omfattar begränsningsalternativ:

- Öka databasens eller den elastiska poolens maxstorlek eller lägga till mer lagringsutrymme. Se [Skala enskilda databasresurser](sql-database-single-database-scale.md) och [Skala elastiska poolresurser](sql-database-elastic-pool-scale.md).
- Om databasen finns i en elastisk pool kan databasen flyttas utanför poolen så att dess lagringsutrymme inte delas med andra databaser.
- Förminska en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioner och arbetare (förfrågningar)

Det maximala antalet sessioner och arbetare bestäms av tjänstnivån och beräkningsstorleken (DTU:er/eDTU:er eller virtuella kärnor). Nya begäranden avvisas när sessions- eller arbetargränser nås och klienter får ett felmeddelande. Även om antalet tillgängliga anslutningar kan styras av programmet, är antalet samtidiga arbetstagare ofta svårare att uppskatta och kontrollera. Detta gäller särskilt under högbelastningsperioder när databasresursgränser nås och arbetare samlas på grund av längre frågor som körs, stora blockeringskedjor eller överdriven frågeparallellisering.

När du stöter på hög sessions- eller arbetaranvändning omfattar begränsningsalternativ:

- Öka tjänstnivån eller beräkningsstorleken för databasen eller den elastiska poolen. Se [Skala enskilda databasresurser](sql-database-single-database-scale.md) och [Skala elastiska poolresurser](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursutnyttjandet för varje fråga om orsaken till ökad arbetsutnyttjande beror på konkurrens om beräkningsresurser. Mer information finns i [Frågejustering/tips .](sql-database-performance-guidance.md#query-tuning-and-hinting)
- Minska [inställningen MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximal grad av parallellism).
- Optimera frågearbetsbelastningen för att minska antalet förekomster och varaktigheten av frågeblockering.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Resursförbrukning efter användararbetsbelastningar och interna processer

Cpu- och minnesförbrukning av användararbetsbelastningar i varje databas rapporteras i [vyerna sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) `avg_cpu_percent` och `avg_memory_usage_percent` [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) i och kolumner. För elastiska pooler rapporteras resursförbrukning på poolnivå i [vyn sys.elastic_pool_resource_stats.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) Processorförbrukning för användararbetsbelastning rapporteras `cpu_percent` också via Azure Monitor-måttet för [enskilda databaser](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) och [elastiska pooler](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) på poolnivå.

Azure SQL Database kräver beräkningsresurser för att implementera grundläggande tjänstfunktioner som hög tillgänglighet och haveriberedskap, säkerhetskopiering och återställning av databaser, övervakning, Query Store, Automatisk justering osv. Systemet avsätter en viss begränsad del av de totala resurserna för dessa interna processer med hjälp av [resursstyrningsmekanismer,](#resource-governance) vilket gör resten av resurserna tillgängliga för användararbetsbelastningar. I tider när interna processer inte använder beräkningsresurser gör systemet dem tillgängliga för användararbetsbelastningar.

Total cpu- och minnesförbrukning per användararbetsbelastning och interna processer i SQL Server-instansen som är värd för en enskild databas `avg_instance_cpu_percent` eller `avg_instance_memory_percent` en elastisk pool rapporteras i [vyerna sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) och [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) i och kolumner. Dessa data rapporteras också `sqlserver_process_core_percent` `sqlserver_process_memory_percent` via måtten och Azure Monitor, för [enskilda databaser](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) och [elastiska pooler](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) på poolnivå.

En mer detaljerad uppdelning av den senaste resursförbrukningen efter användararbetsbelastningar och interna processer rapporteras i [vyerna sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) och [sys.dm_resource_governor_workload_groups_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) Mer information om resurspooler och arbetsbelastningsgrupper som refereras i dessa vyer finns i [Resursstyrning](#resource-governance). Dessa vyer rapporterar om resursutnyttjande av användararbetsbelastningar och specifika interna processer i associerade resurspooler och arbetsbelastningsgrupper.

I samband med prestandaövervakning och felsökning är det viktigt att`avg_cpu_percent`ta `cpu_percent`hänsyn till både **användar-CPU-förbrukning** `sqlserver_process_core_percent`( , ), och **total CPU-förbrukning** av användararbetsbelastningar och interna processer (`avg_instance_cpu_percent`, ).

**Användar-CPU-förbrukning** beräknas som en procentandel av användararbetsbelastningsgränserna i varje tjänstmål. **Användar-CPU-användning** på 100% anger att användararbetsbelastningen har nått gränsen för tjänstmålet. Men när den **totala CPU-förbrukningen** når intervallet 70-100% är det möjligt att se dataflödet för användararbetsbelastningen som planlägger ut och frågefördröjningen ökar, även om den rapporterade **processorförbrukningen** för användare fortfarande ligger betydligt under 100 %. Detta är mer sannolikt att inträffa när du använder mindre servicemål med en måttlig fördelning av beräkningsresurser, men relativt intensiva användararbetsbelastningar, till exempel i [täta elastiska pooler](sql-database-elastic-pool-resource-management.md). Detta kan också inträffa med mindre servicemål när interna processer tillfälligt kräver ytterligare resurser, till exempel när du skapar en ny replik av databasen.

När **den totala CPU-förbrukningen** är hög är begränsningsalternativen desamma som tidigare nämnts och inkluderar optimering av tjänstmålsmål och/eller användararbetsbelastning.

## <a name="resource-governance"></a>Resursstyrning

För att framtvinga resursgränser använder Azure SQL Database en implementering av resursstyrning som baseras på SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), ändrad och utökad för att köra en SQL Server-databastjänst i Azure. På varje SQL Server-instans i tjänsten finns det flera [resurspooler](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) och [arbetsbelastningsgrupper](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group), med resursgränser som anges på både pool- och gruppnivåer för att tillhandahålla en [balanserad databas som en tjänst](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Användararbetsbelastning och interna arbetsbelastningar delas in i separata resurspooler och arbetsbelastningsgrupper. Användararbetsbelastningen på de primära och läsbara sekundära replikerna, `SloSharedPool1` inklusive georepliker, klassificeras i resurspoolen och `UserPrimaryGroup.DBId[N]` arbetsbelastningsgruppen, där `N` står för databas-ID-värdet. Dessutom finns det flera resurspooler och arbetsbelastningsgrupper för olika interna arbetsbelastningar.

Förutom att använda resursflagre för att styra resurser inom SQL Server-processen använder Azure SQL Database också Windows [Job Objects](https://docs.microsoft.com/windows/win32/procthread/job-objects) för resursstyrning på processnivå och [FSRM (Windows File Server Resource Manager)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) för hantering av lagringskvoter.

Azure SQL Database resursstyrning är hierarkisk karaktär. Från uppifrån och ned tillämpas gränser på OS-nivå och på lagringsvolymnivå med hjälp av mekanismer för styrning av operativsystemets resurser och resurschef, sedan på resurspoolnivå med hjälp av resurschef och sedan på arbetsbelastningsgruppsnivå med hjälp av Resurschef. Resursstyrningsgränser som gäller för den aktuella databasen eller den elastiska poolen visas i [vyn sys.dm_user_db_resource_governance.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 

### <a name="data-io-governance"></a>Styrning av data-IO

Data-IO-styrning är en process i Azure SQL Database som används för att begränsa både läsa och skriva fysisk IO mot datafiler i en databas. IOPS-gränser har angetts för varje tjänstnivå för att minimera effekten "bullrig granne", för att ge resursallokering rättvisa i tjänsten med flera innehavare och för att hålla sig inom funktionerna för den underliggande maskinvaran och lagringen.

För enskilda databaser tillämpas arbetsbelastningsgruppgränser för all lagrings-IO mot databasen, medan resurspoolgränser gäller för alla lagrings-IO mot alla databaser i samma SQL Server-instans, inklusive `tempdb` databasen. För elastiska pooler gäller arbetsbelastningsgruppgränser för varje databas i poolen, medan `tempdb` resurspoolgränsen gäller för hela den elastiska poolen, inklusive databasen, som delas mellan alla databaser i poolen. I allmänhet kan det hända att resurspoolgränser inte kan uppnås av arbetsbelastningen mot en databas (antingen enstaka eller poolade), eftersom arbetsbelastningsgruppgränserna är lägre än resurspoolgränser och begränsar IOPS/dataflöde tidigare. Poolgränser kan dock nås av den kombinerade arbetsbelastningen mot flera databaser på samma SQL Server-instans.

Om en fråga till exempel genererar 1 000 IOPS utan IO-resursstyrning, men arbetsbelastningsgruppens maximala IOPS-gräns är inställd på 900 IOPS, kan frågan inte generera mer än 900 IOPS. Men om resurspoolens maximala IOPS-gräns är inställd på 1500 IOPS och den totala IO:n från alla arbetsbelastningsgrupper som är associerade med resurspoolen överstiger 1500 IOPS, kan IO för samma fråga minskas under arbetsgruppsgränsen på 900 IOPS.

IOPS- och dataflödesmin/max-värdena som returneras av [vyn sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) fungerar som gränser/tak, inte som garantier. Dessutom garanterar resursstyrning inte någon specifik lagringsfördröjning. Den bästa uppnåeliga svarstiden, IOPS och dataflöde för en viss användararbetsbelastning beror inte bara på IO-resursstyrningsgränser, utan också på blandningen av IO-storlekar som används och på funktionerna i den underliggande lagringen. SQL Server använder IOs som varierar i storlek mellan 512 KB och 4 MB. För att tillämpa IOPS-gränser redovisas varje IO oavsett storlek, med undantag för databaser med datafiler i Azure Storage. I så fall redovisas IOs som är större än 256 KB som flera 256 KB IOs, för att anpassa sig till Azure Storage IO-redovisning.

För basic-, standard- och general purpose-databaser, som `primary_group_max_io` använder datafiler i Azure Storage, kanske värdet inte kan uppnås om en databas inte har tillräckligt med datafiler för att kumulativt ange det här antalet IOPS, eller om data inte distribueras jämnt mellan filer, eller om prestandanivån för underliggande blobbar begränsar IOPS/dataflöde under resursstyrningsgränsen. På samma sätt, med små logg-IOs `primary_max_log_rate` som genereras av frekventa transaktionsförsetagande, kan värdet inte uppnås av en arbetsbelastning på grund av IOPS-gränsen för den underliggande Azure storage-bloben.

Resursutnyttjandevärden `avg_data_io_percent` `avg_log_write_percent`som och , som rapporteras i [system.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)och [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) vyer, beräknas som procentandelar av maximala resursstyrningsgränser. När andra faktorer än resursstyrning begränsar IOPS/dataflöde är det därför möjligt att se IOPS/dataflöde som planar ut och svarstider ökar när arbetsbelastningen ökar, även om det rapporterade resursutnyttjandet fortfarande är lägre än 100 %. 

Om du vill visa IOPS, dataflöde och svarstid per databasfil använder du funktionen [sys.dm_io_virtual_file_stats().](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Den här funktionen visar all IO mot databasen, inklusive `avg_data_io_percent`bakgrunds-I/O som inte redovisas i , men använder IOPS och dataflöde för den underliggande lagringen och kan påverka observerade lagringsfördröjningar. Funktionen visar också ytterligare svarstid som kan införas av IO-resursstyrning `io_stall_queued_read_ms` för `io_stall_queued_write_ms` läsningar och skrivningar, i respektive kolumnerna.

### <a name="transaction-log-rate-governance"></a>Styrning av transaktionslogghastighet

Styrning av transaktionslogghastighet är en process i Azure SQL Database som används för att begränsa höga inmatningshastigheter för arbetsbelastningar som massinfogning, SELECT INTO och indexversioner. Dessa gränser spåras och tillämpas på undersekundnivå till hastigheten för generering av loggpost, vilket begränsar dataflödet oavsett hur många IOs kan utfärdas mot datafiler.  Genereringsfrekvensen för transaktionsloggar skalas för närvarande linjärt upp till en punkt som är maskinvaruberoende, med den högsta tillåtna logghastigheten är 96 MB/s med vCore-inköpsmodellen. 

> [!NOTE]
> De faktiska fysiska IOs till transaktionsloggfiler styrs inte eller begränsas.

Logghastigheter är inställda så att de kan uppnås och upprätthållas i en mängd olika scenarier, medan det övergripande systemet kan behålla sin funktionalitet med minimerad påverkan på användarbelastningen. Loggfrekvensstyrning säkerställer att säkerhetskopiering av transaktionsloggar håller sig inom publicerade återställnings- och säkerhets-SLA:er.  Den här styrningen förhindrar också en överdriven eftersläpning på sekundära repliker.

När loggposter genereras utvärderas och utvärderas varje åtgärd för om den ska fördröjas för att upprätthålla en maximal önskad logghastighet (MB/s per sekund). Fördröjningarna läggs inte till när loggposterna rensas till lagring, utan logghastighetsstyrning tillämpas under själva logghastighetsgenereringen.

De faktiska logggenereringshastigheterna som införts vid körning kan också påverkas av återkopplingsmekanismer, vilket tillfälligt minskar de tillåtna logghastigheterna så att systemet kan stabiliseras. Hantering av loggfilutrymme, undvika att köra in i ut ur loggutrymmesförhållanden och replikeringsmekanismer för tillgänglighetsgrupper kan tillfälligt minska de totala systemgränserna.

Logghastighetsregulator trafikformning visas via följande vänta typer (exponeras i [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Typ av väntetyp | Anteckningar |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Begränsning av databas |
| POOL_LOG_RATE_GOVERNOR | Poolbegränsning |
| INSTANCE_LOG_RATE_GOVERNOR | Begränsning av instansnivå |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedbackkontroll, fysisk replikering av tillgänglighetsgrupper i Premium/Business Critical håller inte jämna steg |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedback kontroll, begränsa priser för att undvika en ur log utrymme skick |
|||

När du stöter på en logghastighetsgräns som hindrar önskad skalbarhet bör du tänka på följande alternativ:
- Skala upp till en högre servicenivå för att få den maximala logghastigheten på 96 MB/s eller växla till en annan tjänstnivå. Tjänstnivån [Hyperskala](sql-database-service-tier-hyperscale.md) ger 100 MB/s logghastighet oavsett vald tjänstnivå.
- Om data som läses in är tillfälliga, till exempel mellanlagringsdata i en ETL-process, kan de läsas in i tempdb (som är minimalt inloggad). 
- För analytiska scenarier läser du in den i en klusterkolonn som omfattas av tabellen. Detta minskar den logghastighet som krävs på grund av komprimering. Den här tekniken ökar CPU-användningen och är endast tillämplig på datauppsättningar som drar nytta av klustrade columnstore-index. 

## <a name="next-steps"></a>Nästa steg

- Information om allmänna Azure-gränser finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om DTI:er och eDU:er finns i [DTI: er och eDTI: er](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Information om tempdb-storleksbegränsningar finns [i TempDB i Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
