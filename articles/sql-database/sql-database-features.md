---
title: Jämför med Azure SQL Database-funktioner | Microsoft Docs
description: Den här artikeln jämförs funktionerna i SQL Server som är tillgängliga i olika varianter av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/02/2019
ms.openlocfilehash: 2f7f317f11bba96e17791ed751c60099457a299a
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002594"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Jämförelse av funktioner: Azure SQL Database jämfört med SQLServer

Azure SQL-databas delar en gemensam kodbas med SQL Server. Funktioner i SQL Server som stöds av Azure SQL Database beror på vilken typ av Azure SQL-databas som du skapar. Med Azure SQL Database kan du antingen skapa en databas som en del av en [hanterad instans](sql-database-managed-instance.md) eller så kan du skapa en databas som är en del av logisk server och du kan också placeras i en elastisk pool.

Microsoft fortsätter att lägga till funktioner till Azure SQL Database. Gå till webbsida för tjänstuppdateringar för Azure för de senaste uppdateringarna som använder filtren:

- Filtrerade till [SQL Database-tjänsten](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrerade till allmän tillgänglighet [meddelanden (GA)](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) för SQL Database-funktioner.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Stöd för SQL Server-funktionen i Azure SQL Database

I följande tabell visas de viktigaste funktionerna i SQL Server och innehåller information om huruvida funktionen helt eller delvis stöds och en länk till mer information om funktionen.

| **SQL-funktionen** | **Stöds i databasen/logisk Azure SQL-Server** | **Stöds i Azure SQL-databas/hanterad instans** |
| --- | --- | --- |
| [Aktiv geo-replikering](sql-database-active-geo-replication.md) | Ja – generell användning och affärskritisk tjänstnivåer endast| Nej |
| [Automatisk redundans grupper](sql-database-auto-failover-group.md) | Ja – generell användning och affärskritisk tjänstnivåer endast| Ja (förhandsversion)|
| [Alltid krypterad](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Ja – Se [certifikatarkiv](sql-database-always-encrypted.md) och [Key vault](sql-database-always-encrypted-azure-key-vault.md) | Ja – Se [certifikatarkiv](sql-database-always-encrypted.md) och [Key vault](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On-Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [Ansluta en databas](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nej | Nej |
| [Programroller](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Ja | Ja |
|[Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Ja](sql-database-auditing.md)| [Ja](sql-database-managed-instance-auditing.md) |
| [Automatisk säkerhetskopiering](sql-database-automated-backups.md) | Ja | Ja |
| [Automatisk justering (markörplan)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)| [Ja](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatisk justering (index)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)| Nej |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | Ja | Ja |
| [BACPAC-fil (exportera)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ja – Se [SQL Database-export](sql-database-export.md) | Ja |
| [BACPAC-fil (import)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ja – Se [SQL Database-import](sql-database-import.md) | Ja |
| [BACKUP-kommandot](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nej, endast systeminitierade automatiska säkerhetskopior – Se [automatiska säkerhetskopior](sql-database-automated-backups.md) | Systeminitierade automatiska säkerhetskopieringar och användaren initierade endast kopiering säkerhetskopior – Se [säkerhetskopiera skillnader](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Inbyggda funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta – se enskilda funktioner | Ja – Se [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Sammanställning av ändringsdata](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nej | Ja |
| [Spårning av ändringar](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Ja |Ja |
| [Sortering - databas](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | Ja | Ja |
| [Sortering - server/instans](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | Nej | [Ja (förhandsversion)](https://docs.microsoft.com/azure/sql-database/scripts/sql-managed-instance-create-powershell-azure-resource-manager-template)|
| [Columnstore-index](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Ja – [Premium-nivån, Standard-nivån – S3 och senare, nivån generell användning och affärskritisk nivåer](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Ja |
| [CLR (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nej | Ja – Se [CLR-skillnader](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Ja | Ja |
| [Inneslutna användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Ja | Ja |
| [Kontroll av flödesspråk](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Ja | Ja |
| [Frågor mellan databaser](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nej, se [elastiska frågor](sql-database-elastic-query-overview.md) | Ja, plus [elastiska frågor](sql-database-elastic-query-overview.md) |
| [Transaktioner över flera databaser](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nej | Ja – Se [länkad server skillnader](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#linked-servers) |
| [Markörer](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Ja |Ja |
| [Datakomprimering](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Ja |Ja |
| [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nej | Ja |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Ja | Ja |
| [Databasspegling](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nej | Nej |
| [Databaskonfigurationsinställningar](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Ja | Ja |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nej | Nej |
| [Databasögonblicksbilder](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nej | Nej |
| [Datatyper](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Ja |Ja |
| [DBCC-uttryck](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | De flesta – se enskilda uttryck | Ja – Se [DBCC skillnader](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL-instruktionerna](https://docs.microsoft.com/sql/t-sql/statements/statements) | De flesta – se enskilda uttryck | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [DDL-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Endast databas |  Ja |
| [Distribuerade partition vyer](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Nej | Ja |
| [Distribuerade transaktioner - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Nej, se [elastiska transaktioner](sql-database-elastic-transactions-overview.md) |  Nej, se [elastiska transaktioner](sql-database-elastic-transactions-overview.md) |
| [DML-instruktioner](https://docs.microsoft.com/sql/t-sql/queries/queries) | Ja | Ja |
| [DML-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | De flesta – se enskilda uttryck |  Ja |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | De flesta – se enskilda DMV: er |  Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
|[Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Ja](sql-database-dynamic-data-masking-get-started.md)| [Ja](sql-database-dynamic-data-masking-get-started.md) |
| [Elastiska pooler](sql-database-elastic-pool.md) | Ja | Inbyggda – en enda hanterad instans kan ha flera databaser som delar samma pool av resurser |
| [Händelseaviseringar](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Nej, se [aviseringar](sql-database-insights-alerts-portal.md) | Nej |
| [Uttryck](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Ja | Ja |
| [Utökade händelser](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Vissa – Se [utökade händelser i SQL-databas](sql-database-xevent-db-diff-from-svr.md) | Ja – Se [utökade händelser skillnader](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nej | Nej |
[Filer och filgrupper](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Endast primär filgrupp | Ja |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nej | Nej |
| [Fulltextsökning](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Orddelare från tredje part stöds inte |Orddelare från tredje part stöds inte |
| [Funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta – se enskilda funktioner | Ja – Se [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [GEO-återställning](sql-database-recovery-using-backups.md#geo-restore) | Ja – generell användning och affärskritisk tjänstnivåer endast | Nej, du kan återställa COPY_ONLY fullständiga säkerhetskopieringar som utföras med jämna mellanrum – se [säkerhetskopiera skillnader](sql-database-managed-instance-transact-sql-information.md#backup) och [återställa skillnader](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Bearbeta diagram](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Ja | Ja |
| [Minnesintern optimering](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Ja – [Premium och affärskritisk nivåer](sql-database-in-memory.md) | Ja – [företag affärskritisk nivå](sql-database-managed-instance.md) |
| [Stöd för JSON-data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Ja](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) | [Ja](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) |
| [Språkelement](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | De flesta – se enskilda element |  Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Länkade servrar](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nej, se [elastisk fråga](sql-database-elastic-query-horizontal-partitioning.md) | Endast för SQLServer och SQL-databas |
| [Loggöverföring](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |[Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nej | Nej |
| [Minimal loggning i massimport](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nej | Nej |
| [Ändra systemdata](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nej | Ja |
| [Indexåtgärder online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Ja | Ja |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Nej|Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Ja|Ja|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Nej|Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Nej|Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Ja|Ja|
| [Operatörer](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | De flesta – se enskilda operatorer |Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Partitionering](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Ja | Ja |
| [Tidpunkt för återställning av databasen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Ja – generell användning och affärskritisk tjänstnivåer bara – se [återställning av SQL-databas](sql-database-recovery-using-backups.md#point-in-time-restore) | Ja – Se [återställning av SQL-databas](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nej | Nej |
| [Principbaserad hantering](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nej | Nej |
| [Predikat](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Ja | Ja |
| [Frågemeddelanden](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | Nej | Ja |
| [R-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Förhandsversionen; Se [vad är nytt i machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Nej |
| [Resursstyrning](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nej | Ja |
| [RESTORE-uttryck](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nej | Ja – Se [återställa skillnader](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Återställa databasen från en säkerhetskopia](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Data från automatiska säkerhetskopieringar endast – se [återställning av SQL-databas](sql-database-recovery-using-backups.md) | Data från automatiska säkerhetskopieringar – Se [SQL Database recovery](sql-database-recovery-using-backups.md) och fullständiga säkerhetskopieringar – Se [säkerhetskopiera skillnader](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Ja | Ja |
| [Semantisk sökning](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nej | Nej |
| [Sekvensnummer](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Ja | Ja |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nej | Ja – Se [Service Broker-skillnader](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Inställningar för serverkonfiguration](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nej | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Ange uttryck](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | De flesta – se enskilda uttryck | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Ja | Ja |
| [Rumslig](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Ja | Ja |
| [SQL Data Sync](sql-database-get-started-sql-data-sync.md) | Ja | Nej |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Nej, se [elastiska jobb](sql-database-elastic-jobs-getting-started.md) | Ja – Se [SQL Server Agent-skillnader](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Nej, se [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Nej, se [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server-granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Nej, se [SQL Database-granskning](sql-database-auditing.md) | Ja – Se [granskning skillnader](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Ja | Ja |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Ja, med en hanterade SSIS i Azure Data Factory (ADF)-miljö, var paket lagras i SSISDB med Azure SQL Database och körs på Azure SSIS Integration Runtime (IR), se [skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Jämförelse mellan SSIS-funktioner i logisk SQL Database-server och Managed Instance finns [logisk jämför SQL Database-server och Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). | Ja, med en hanterade SSIS i Azure Data Factory (ADF)-miljö, var paket lagras i SSISDB med Managed Instance och körs på Azure SSIS Integration Runtime (IR), se [skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Jämförelse mellan SSIS-funktioner i SQL Database och Managed Instance finns [logisk jämför SQL Database-server och Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Ja | Ja |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Ja | Ja |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Nej, se [utökade händelser](sql-database-xevent-db-diff-from-svr.md) | Ja |
| [SQL Server-replikering](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Prenumerant för transaktions-och ögonblicksbildsreplikering endast](sql-database-cloud-migrate.md) | Ja (allmänt tillgänglig förhandsversion) – [replikering med SQL Database Managed Instance](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQLServer Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nej, [Se Power BI](https://docs.microsoft.com/power-bi/) | Nej, [Se Power BI](https://docs.microsoft.com/power-bi/) |
| [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Ja | Ja |
| [Systemlagrade funktioner](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | De flesta – se enskilda funktioner | Ja – Se [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Systemlagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Vissa – se enskilda lagrade procedurer | Ja – Se [lagrade procedurer, funktioner, utlöser skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Systemtabeller](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Vissa – se enskilda tabeller | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Systemkatalogvyer](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Vissa – se enskilda vyer | Ja – Se [T-SQL-skillnader](sql-database-managed-instance-transact-sql-information.md) |
| [Temporary tables](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) (Temporära tabeller) | Lokal och databasbegränsade globala tillfälliga tabeller | Lokal och instans-omfattande globala tillfälliga tabeller |
| [Temporala tabeller](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Ja](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) | [Ja](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) |
|Hotidentifiering|  [Ja](sql-database-threat-detection.md)|[Ja](sql-database-managed-instance-threat-detection.md)|
| [Spårningsflaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Nej | Nej |
| [Variabler](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Ja | Ja |
| [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Ja – generell användning och affärskritisk tjänstnivåer endast| [Ja](transparent-data-encryption-azure-sql.md) |
[Virtuellt nätverk](../virtual-network/virtual-networks-overview.md) | Delvis – Se [slutpunkter för virtuellt nätverk](sql-database-vnet-service-endpoint-rule-overview.md) | Ja, Resource Manager-modellen |
| [Windows Server-Redundansklustring](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Katastrofåterställning diskuteras i [översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [XML-index](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- Information om en hanterad instans finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
