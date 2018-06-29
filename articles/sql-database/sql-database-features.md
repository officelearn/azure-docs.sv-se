---
title: Azure SQL Database-funktionsjämförelse | Microsoft Docs
description: Den här artikeln jämförs funktioner i SQL Server som är tillgängliga i olika varianter av Azure SQL Database.
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 1dea3affa06da47f22a3e7cca6c48e6c7a288b1d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083370"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Funktionsjämförelse: Azure SQL Database och SQL Server 

Azure SQL Database delar en gemensam kodbas med SQL Server. Funktioner i SQL Server som stöds av Azure SQL Database beror på vilken typ av Azure SQL-databas som du skapar. Med Azure SQL Database, kan du antingen skapa en databas som en del av en [hanterade instans](sql-database-managed-instance.md) (för närvarande i förhandsversion) eller så kan du skapa en databas som är en del av logisk server och du kan också placeras i en elastisk pool. 

Microsoft fortsätter att lägga till funktioner i Azure SQL Database. Finns på webbsidan för uppdateringar av tjänsten för Azure efter de senaste uppdateringarna med dessa filter:

* Filtrerade till [SQL Database-tjänsten](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrerade till allmän tillgänglighet [meddelanden (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) för SQL Database-funktioner.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Stöd för SQL Server-funktionen i Azure SQL Database

I följande tabell visar de viktigaste funktionerna i SQL Server och innehåller information om huruvida funktionen helt eller delvis stöds och en länk till mer information om funktionen. 

| **SQL-funktion** | **Stöds i databasen/logisk Azure SQL-Server** | **Stöds i databasen/hanterad Azure SQL-instans (förhandsgranskning)** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Ja - finns [certifikatarkivet](sql-database-always-encrypted.md) och [Key vault](sql-database-always-encrypted-azure-key-vault.md) | Ja - finns [certifikatarkivet](sql-database-always-encrypted.md) och [Key vault](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On-Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [Ansluta en databas](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nej | Nej |
| [Programroller](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Ja | Ja |
|[Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Ja](sql-database-auditing.md)| [Ja](sql-database-managed-instance-auditing.md) |
| [Automatisk säkerhetskopiering](sql-database-automated-backups.md) | Ja | Ja |
| [Automatisk justering (markörplan)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)| [Ja](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatisk justering (index)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)| Nej |
| [BACPAC fil (exportera)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ja - finns [SQL Database-export](sql-database-export.md) | Nej |
| [BACPAC fil (importera)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ja - finns [Importera SQL-databas](sql-database-import.md) | Nej |
| [BACKUP-kommandot](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nej, endast system-initierad automatiska säkerhetskopieringar - finns [automatisk säkerhetskopiering](sql-database-automated-backups.md) | System-initierad automatiska säkerhetskopieringar och användaren initierade endast kopiering säkerhetskopieringar - Se [säkerhetskopiera skillnader](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Inbyggda funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta - finns enskilda funktioner | Ja - finns [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Registrering av ändringsdata](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nej | Ja |
| [Spårning av ändringar](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Ja |Ja |
| [Instruktioner för sortering](https://docs.microsoft.com/sql/t-sql/statements/collations) | Ja | Ja |
| [Columnstore-index](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Ja - [Premium-nivån, standardnivån - S3 och senare generella nivå och företag kritiska nivåer](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Ja |
| [CLR (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nej | Ja - finns [CLR skillnader](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Ja | Ja |
| [Inneslutna användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Ja | Ja |
| [Kontroll av flödet nyckelord](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Ja | Ja |
| [Mellan databasfrågor](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Finns inte - [elastisk frågor](sql-database-elastic-query-overview.md) | Ja, plus [elastisk frågor](sql-database-elastic-query-overview.md) |
| [Transaktioner över flera databaser](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nej | Ja - finns [länkad server skillnader](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#linked-servers) |
| [Markörer](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Ja |Ja | 
| [Datakomprimering](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Ja |Ja |
| [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nej | Ja |
| [Data migrering Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Ja | Ja |
| [Databasspegling](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nej | Nej |
| [Konfigurationsinställningar för databasen](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Ja | Ja |
| [Data Quality Services DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nej | Nej |
| [Databasögonblicksbilder](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nej | Nej |
| [Datatyper](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Ja |Ja |
| [DBCC-instruktioner](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | De flesta - finns enskilda uttryck | Ja - finns [DBCC skillnader](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL-instruktioner](https://docs.microsoft.com/sql/t-sql/statements/statements) | De flesta - finns enskilda uttryck | Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [DDL-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Endast databas |  Ja |
| [Partitionen för distribuerade vyer](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Nej | Ja |
| [Distribuerade transaktioner - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Finns inte - [elastisk transaktioner](sql-database-elastic-transactions-overview.md) |  Finns inte - [elastisk transaktioner](sql-database-elastic-transactions-overview.md) |
| [DML-instruktioner](https://docs.microsoft.com/sql/t-sql/queries/queries) | Ja | Ja |
| [DML-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | De flesta - finns enskilda uttryck |  Ja |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | De flesta - finns enskilda av DMV: er |  Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
|[Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Ja](sql-database-dynamic-data-masking-get-started.md)| [Ja](sql-database-dynamic-data-masking-get-started.md) |
| [Elastiska pooler](sql-database-elastic-pool.md) | Ja | Inbyggda – en enda instans hanteras kan ha flera databaser som delar samma pool av resurser |
| [Händelseaviseringar](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Finns inte - [aviseringar](sql-database-insights-alerts-portal.md) | Ja |
| [Uttryck](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Ja | Ja |
| [Utökade händelser](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Vissa - Se [utökade händelser i SQL-databas](sql-database-xevent-db-diff-from-svr.md) | Ja - finns [utökade händelser skillnader ](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nej | Nej |
[Filer och filgrupper](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Endast primära filgruppen | Ja |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nej | Nej |
| [Fulltextsökning](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Tredjeparts-funnits stöds inte |Tredjeparts-funnits stöds inte |
| [Funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta - finns enskilda funktioner | Ja - finns [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [GEO-återställning](sql-database-recovery-using-backups.md#geo-restore) | Ja | Nej – du kan återställa COPY_ONLY fullständiga säkerhetskopieringar som utföras med jämna mellanrum - finns [säkerhetskopiera skillnader](sql-database-managed-instance-transact-sql-information.md#backup) och [återställa skillnader](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Geo-replikering](sql-database-geo-replication-overview.md) | Ja | Nej |
| [Diagrammet bearbetning](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Ja | Ja |
| [Minnesintern optimering](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Ja - [Premium och företag kritiska nivåer](sql-database-in-memory.md) | Nej |
| [Stöd för JSON-data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Ja](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) | [Ja](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) |
| [Språkelement](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | De flesta - finns i enskilda element |  Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Länkade servrar](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Finns inte - [elastisk fråga](sql-database-elastic-query-horizontal-partitioning.md) | Endast för SQLServer och SQL-databas |
| [Loggöverföring](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |[Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nej | Nej |
| [Minimal loggning i massimport](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nej | Nej |
| [Ändra systemdata](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nej | Ja |
| [Indexåtgärder online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Ja | Ja |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Nej|Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Ja|Ja|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Nej|Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Nej|Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Ja|Ja|
| [Operatörer](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | De flesta - finns i enskilda operatorer |Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Partitionering](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Ja | Ja |
| [Återställning vid tidpunkt databas](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Ja - finns [återställning av SQL-databasen](sql-database-recovery-using-backups.md#point-in-time-restore) | Ja - finns [återställning av SQL-databasen](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nej | Nej |
| [Principbaserad hantering](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nej | Nej |
| [Predikat](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Ja | Ja |
| [R-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Förhandsversionen; Se [vad är nytt i machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Nej |
| [Resursstyrningen](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nej | Ja |
| [ÅTERSTÄLLA rapporter](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nej | Ja - finns [återställa skillnader](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Återställa databasen från en säkerhetskopia](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Se från automatisk säkerhetskopiering endast - [återställning av SQL-databasen](sql-database-recovery-using-backups.md) | Automatisk säkerhetskopiering – Se [SQL databasåterställning](sql-database-recovery-using-backups.md) och fullständiga säkerhetskopieringar - Se [säkerhetskopiera skillnader](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Ja | Ja |
| [Semantiska sökning](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nej | Nej |
| [Sekvensnummer](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Ja | Ja |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nej | Ja - finns [Service Broker skillnader](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Server-konfigurationsinställningar](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nej | Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Set-instruktioner](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | De flesta - finns enskilda uttryck | Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Ja | Ja |
| [Rumslig](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Ja | Ja |
| [SQL-datasynkronisering](sql-database-get-started-sql-data-sync.md) | Ja | Nej |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | Ja | Ja |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Finns inte - [elastiska jobb](sql-database-elastic-jobs-getting-started.md) | Ja - finns [skillnader i SQL Server Agent](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Finns inte - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Finns inte - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server-granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Finns inte - [SQL Database auditing](sql-database-auditing.md) | Ja - finns [granskning skillnader](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Ja | Ja |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Ja, med en hanterad SSIS i Azure Data Factory (ADM)-miljö där paket lagras i SSISDB hos Azure SQL Database och körs på Azure SSIS Integration Runtime (IR), se [skapa Azure-SSIS-IR i ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Om du vill jämföra SSIS-funktioner i SQL-databasen och hanteras instansen finns [jämför SQL-databasen och hanteras instansen (förhandsgranskning)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). | Ja, med en hanterad SSIS i Azure Data Factory (ADM)-miljö där paket lagras i SSISDB hanteras instans som värd och körs på Azure SSIS Integration Runtime (IR), se [skapa Azure-SSIS-IR i ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Om du vill jämföra SSIS-funktioner i SQL-databasen och hanteras instansen finns [jämför SQL-databasen och hanteras instansen (förhandsgranskning)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Ja | Ja |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Ja | Ja |
| [SQL Server-versioner](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Finns inte - [utökade händelser](sql-database-xevent-db-diff-from-svr.md) | Ja |
| [SQL Server-replikering](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Prenumerant för transaktions-och ögonblicksbildsreplikering endast](sql-database-cloud-migrate.md) | Nej |
| [SQLServer Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nej - [finns i Power BI](https://docs.microsoft.com/power-bi/) | Nej - [finns i Power BI](https://docs.microsoft.com/power-bi/) |
| [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Ja | Ja |
| [Systemfunktioner lagras](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | De flesta - finns enskilda funktioner | Ja - finns [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Systemets lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Vissa - se enskilda lagrade procedurer | Ja - finns [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Systemtabeller](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Vissa - se enskilda tabeller | Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [System katalogvyer](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Vissa - se enskilda vyer | Ja - finns [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Temporary tables](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) (Temporära tabeller) | Lokala och databas-omfattande globala temporära tabeller | Lokala och instans omfång globala temporära tabeller |
| [Temporala tabeller](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Ja](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) | [Ja](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) |
|Hotidentifiering|  [Ja](sql-database-threat-detection.md)|[Ja](sql-database-managed-instance-threat-detection.md)|
| [Spårningsflaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Nej | Nej |
| [Variabler](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Ja | Ja |
| [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Ja | Partiell endast med kryptering som hanteras av tjänsten |
[Virtuella nätverk](../virtual-network/virtual-networks-overview.md) | Partiell - finns [VNET-slutpunkter](sql-database-vnet-service-endpoint-rule-overview.md) | Ja, Resource Manager-modellen |
| [Windows Server Failover-kluster](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [XML-index](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- Information om en hanterad instans finns [vad är en hanterad instans?](sql-database-managed-instance.md).
