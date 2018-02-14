---
title: "Azure SQL Database-funktionsjämförelse | Microsoft Docs"
description: "Den här artikeln jämförs funktionerna i SQL Server och Azure SQL Database och visar dessa skillnader."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/08/2018
ms.author: carlrab
ms.openlocfilehash: dc9a7fa0a7fa0e029f71510cc516496ed12a6274
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Funktionsjämförelse: Azure SQL Database och SQL Server 

Azure SQL Database delar en gemensam kodbas med SQL Server och på nivån databasen stöder de flesta av samma funktioner. Större funktion skillnaderna mellan Azure SQL Database och SQL Server finns på instansnivå. 

Vi fortsätter att lägga till funktioner till Azure SQL Database. Så vi rekommenderar att du besöker vår webbsida för tjänstuppdateringar för Azure och dess filter:

* Filtrerade till [SQL Database-tjänsten](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrerade till allmän tillgänglighet [meddelanden (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) för SQL Database-funktioner.

## <a name="sql-server-and-sql-database-feature-support"></a>Stöder SQL Server och SQL-databas

I följande tabell visar de viktigaste funktionerna i SQL Server och innehåller information om huruvida varje viss funktion stöds och en länk till mer information om funktionen. Om du vill veta vilka Transact-SQL-skillnader du ska ha i åtanke när du migrerar en befintlig databaslösning kan du läsa [Azure SQL Database Transact-SQL skillnader](sql-database-transact-sql-information.md).


| **SQL Server-funktionen** | **Stöds i Azure SQL-databas** | 
| --- | --- |  
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Ja - finns [certifikatarkivet](sql-database-always-encrypted.md) och [Key vault](sql-database-always-encrypted-azure-key-vault.md)|
| [AlwaysOn-Tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Hög tillgänglighet ingår i varje databas. Se [hög tillgänglighet och Azure SQL Database](sql-database-high-availability.md). Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-geo-replication-overview.md) |
| [Ansluta en databas](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nej |
| [Programroller](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Ja |
|[Granskning](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Ja](sql-database-auditing.md)|
| [Automatisk inställning](/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)|
| [BACPAC fil (exportera)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ja - finns [SQL Database-export](sql-database-export.md) |
| [BACPAC fil (importera)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ja - finns [Importera SQL-databas](sql-database-import.md) |
| [BACKUP-kommandot](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Finns inte - [automatisk säkerhetskopiering](sql-database-automated-backups.md) |
| [Inbyggda funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta - finns enskilda funktioner |
| [Registrering av ändringsdata](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nej |
| [Spårning av ändringar](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Ja |
| [Instruktioner för sortering](https://docs.microsoft.com/sql/t-sql/statements/collations) | Ja |
| [Columnstore-index](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Ja - [endast Premium edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [CLR (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nej |
| [Inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Ja |
| [Inneslutna användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Ja |
| [Kontroll av flödet nyckelord](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Ja |
| [Mellan databasfrågor](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Delvis – se [Elastiska frågor](sql-database-elastic-query-overview.md) |
| [Markörer](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Ja | 
| [Datakomprimering](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Ja |
| [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nej |
| [Databasspegling](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nej |
| [Konfigurationsinställningar för databasen](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Ja |
| [Data Quality Services DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nej |
| [Databasögonblicksbilder](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nej |
| [Datatyper](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Ja |  
| [DBCC-instruktioner](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | De flesta - finns enskilda uttryck |
| [DDL-instruktioner](https://docs.microsoft.com/sql/t-sql/statements/statements) | Ja |
| [DDL-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Endast databas |
| [Distribuerade transaktioner - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Finns inte - [elastisk transaktioner](sql-database-elastic-transactions-overview.md) |
| [DML-instruktioner](https://docs.microsoft.com/sql/t-sql/queries/queries) | Ja |
| [DML-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | De flesta - finns enskilda uttryck | 
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Vissa - se enskilda av DMV: er |
|[Dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking)|[Ja](sql-database-dynamic-data-masking-get-started.md)|
| [Händelseaviseringar](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Finns inte - [aviseringar](sql-database-insights-alerts-portal.md) |
| [Uttryck](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Ja |
| [Utökade händelser](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Vissa - Se [utökade händelser i SQL-databas](sql-database-xevent-db-diff-from-svr.md) |
| [utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nej |
| [Filer och filgrupper](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Endast primära filgruppen |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nej |
| [Fulltextsökning](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | Tredjeparts-funnits stöds inte |
| [Funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta - finns enskilda funktioner |
| [Diagrammet bearbetning](/sql/relational-databases/graphs/sql-graph-overview) | Ja |
| [Minnesintern optimering](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Ja - [endast Premium edition](sql-database-in-memory.md) |
| [Stöd för JSON-data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | Ja |
| [Språkelement](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | De flesta - finns i enskilda element |  
| [Länkade servrar](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Finns inte - [elastisk fråga](sql-database-elastic-query-horizontal-partitioning.md) |
| [Loggöverföring](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Hög tillgänglighet ingår i varje databas. Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-geo-replication-overview.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nej |
| [Minimal loggning i massimport](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nej |
| [Ändra systemdata](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nej |
| [Indexåtgärder online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Ja |
| [Operatörer](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | De flesta - finns i enskilda operatorer |
| [Återställning vid tidpunkt databas](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Ja - finns [återställning av SQL-databasen](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nej |
| [Principbaserad hantering](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nej |
| [Predikat](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Ja |
| [R-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Förhandsversionen; Se [vad är nytt i machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  |
| [Resursstyrningen](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nej |
| [ÅTERSTÄLLA rapporter](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nej | 
| [Återställa databasen från en säkerhetskopia](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Se från inbyggda säkerhetskopieringar endast - [återställning av SQL-databasen](sql-database-recovery-using-backups.md) |
| [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Ja |
| [Semantiska sökning](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nej |
| [Sekvensnummer](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Ja |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nej |
| [Server-konfigurationsinställningar](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nej |
| [Set-instruktioner](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | De flesta - finns enskilda uttryck 
| [Rumslig](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Ja |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Finns inte - [elastiska jobb](sql-database-elastic-jobs-getting-started.md) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Se [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server-granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Finns inte - [SQL Database auditing](sql-database-auditing.md) |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Ja - finns [Lift och SKIFT SQL Serverintegration Services-arbetsbelastningar till molnet](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Ja |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Finns inte - [utökade händelser](sql-database-xevent-db-diff-from-svr.md) |
| [SQL Server-replikering](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Prenumerant för transaktions-och ögonblicksbildsreplikering endast](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nej |
| [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Ja |
| [Systemfunktioner lagras](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Vissa - se enskilda funktioner |
| [Systemets lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Vissa - se enskilda lagrade procedurer |
| [Systemtabeller](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Vissa - se enskilda tabeller |
| [System katalogvyer](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Vissa - se enskilda vyer |
| [Table partitioning](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) (Tabellpartitionering) | Ja - endast primära filgruppen |
| [Temporary tables](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) (Temporära tabeller) | Lokala och databas-omfattande globala temporära tabeller endast |
| [Temporala tabeller](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | Ja |
| [Variabler](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Ja | 
| [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Ja |
| [Windows Server Failover-kluster](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Hög tillgänglighet ingår i varje databas. Katastrofåterställning diskuteras i [översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-geo-replication-overview.md) |
| [XML-index](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Ja |

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- Mer information om stöd för och skillnader jämfört med Transact-SQL finns i [Azure SQL Database Transact-SQL skillnader](sql-database-transact-sql-information.md).
