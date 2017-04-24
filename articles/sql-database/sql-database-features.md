---
title: "Översikt över funktioner i Azure SQL Database | Microsoft Docs"
description: "Den här sidan innehåller en översikt över Azure SQL Database logiska servrar och databaser och innehåller en matris över funktionsstöds med länkar för varje listad funktion."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: features
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a1ede93b5aacf0d8a5bcf83f208f72be328ee72f
ms.lasthandoff: 04/15/2017


---
# <a name="azure-sql-database-features"></a>Azure SQL Database-funktioner

I följande tabeller visas de viktigaste funktionerna i Azure SQL Database och motsvarande funktioner i SQL Server. Dessutom visas information om huruvida det finns stöd för de olika funktionerna och en länk till mer information om funktionen på respektive plattform. Om du vill veta vilka Transact-SQL-skillnader du ska ha i åtanke när du migrerar en befintlig databaslösning kan du läsa [Azure SQL Database Transact-SQL skillnader](sql-database-transact-sql-information.md).

Vi fortsätter att lägga till funktioner till Azure SQL Database. Så vi rekommenderar att du besöker vår webbsida för tjänstuppdateringar för Azure och dess filter:

* Filtrerade till [SQL Database-tjänsten](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrerade till allmän tillgänglighet [meddelanden (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) för SQL Database-funktioner.

| **Funktion** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| Aktiv geo-replikering | Stöds inte – se [AlwaysOn-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Stöds](sql-database-geo-replication-overview.md)
| Alltid krypterad | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Stöds – se [Certifikatarkiv](sql-database-always-encrypted.md) och [Nyckelvalv](sql-database-always-encrypted-azure-key-vault.md)|
| AlwaysOn-tillgänglighetsgrupper | [Stöds](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Finns inte stöd för – [aktiv geo-replikering](sql-database-geo-replication-overview.md) |
| Ansluta en databas | [Stöds](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Stöds inte |
| Programroller | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| Autoskala | Stöds inte | Stöds – se [Tjänstnivåer](sql-database-service-tiers.md) |
| Azure Active Directory | Stöds inte | [Stöds](sql-database-aad-authentication.md) |
| Azure Data Factory | [Stöds](../data-factory/data-factory-introduction.md) | [Stöds](../data-factory/data-factory-introduction.md) |
| Granskning | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Stöds](sql-database-auditing.md) |
| BACPAC-fil (exportera) | [Stöds](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [Stöds](sql-database-export.md) |
| BACPAC-fil (importera) | [Stöds](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [Stöds](sql-database-import.md) |
| SÄKERHETSKOPIERING | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Stöds inte |
| Inbyggda funktioner | [Stöds](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta – se [enskilda funktioner] (https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Registrering av ändringsdata | [Stöds](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Stöds inte |
| Spårning av ändringar | [Stöds](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [Stöds](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Sortering av uttryck | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/collations) | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Columnstore-index | [Stöds](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Endast Premium edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| CLR (Common language runtime) | [Stöds](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Stöds inte |
| Inneslutna databaser | [Stöds](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [Stöds](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| Inneslutna användare | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [Stöds](sql-database-manage-logins.md#non-administrator-users) |
| Kontroll av nyckelord för flödesspråk | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| Frågor över flera databaser | [Stöds](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Delvis – se [Elastiska frågor](sql-database-elastic-query-overview.md) |
| Markörer | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| Datakomprimering | [Stöds](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [Stöds](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| Säkerhetskopior av databasen | [Hanteras av användare](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [Hanteras av SQL Database-tjänsten](sql-database-automated-backups.md) |
| Database-mail | [Stöds](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Stöds inte |
| Databasspegling | [Stöds](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Stöds inte |
| Inställningar för databaskonfiguration | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [Stöds](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Stöds inte |
| Ögonblicksbilder av databas | [Stöds](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Stöds inte |
| Datatyper | [Stöds](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [Stöds](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| DBCC-uttryck | [Stöds](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | De flesta – se [enskilda uttryck](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) |
| DDL-uttryck | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/statements) | De flesta – se [enskilda uttryck](https://docs.microsoft.com/sql/t-sql/statements/statements)
| DDL-utlösare | [Stöds](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [Endast databas](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| Distribuerade transaktioner | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Begränsad endast intra-SQL Database-scenarier |
| DML-uttryck | [Stöds](https://docs.microsoft.com/sql/t-sql/queries/queries) | De flesta – se [enskilda uttryck] (https://docs.microsoft.com/sql/t-sql/queries/queries) |
| DML-utlösare | [Stöds](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [Stöds](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMV | [Alla](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Vissa – se [enskilda DMV:er](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) |
| Elastiska pooler | Stöds inte | [Stöds](sql-database-elastic-pool.md) |
| Elastiska jobb | Finns inte stöd för – [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | [Stöds](sql-database-elastic-jobs-getting-started.md) | 
| Elastiska frågor | Finns inte stöd för – [Frågor över flera databaser](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | [Stöds](sql-database-elastic-query-overview.md) |
| Händelseaviseringar | [Stöds](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [Stöds](sql-database-insights-alerts-portal.md) |
| Uttryck | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| Utökade händelser | [Stöds](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Vissa – se [enskilda händelser](sql-database-xevent-db-diff-from-svr.md) |
| Utökade lagrade procedurer | [Stöds](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Stöds inte |
| Filer och filgrupper | [Stöds](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [Endast primär](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| FileStream | [Stöds](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Stöds inte |
| Fulltextsökning | [Stöds](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [Orddelare från tredje part stöds inte](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| Funktioner | [Stöds](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta – se [enskilda funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Minnesintern optimering | [Stöds](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Endast Premium edition](sql-database-in-memory.md) |
| Jobb | Se [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Se [Elastiska jobb](sql-database-elastic-jobs-getting-started.md) |
| Stöd för JSON-data | [Stöds](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Stöds](sql-database-json-features.md) |
| Språkelement | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | De flesta – se [enskilda element](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) |  
| Länkade servrar | [Stöds](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Finns inte stöd för – [elastisk fråga](sql-database-elastic-query-horizontal-partitioning.md) |
| Loggöverföring | [Stöds](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Finns inte stöd för – [aktiv geo-replikering](sql-database-geo-replication-overview.md) |
| Master Data Services (MDS) | [Stöds](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Stöds inte |
| Minimal loggning i massimport | [Stöds](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Stöds inte |
| Ändra systemdata | [Stöds](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Stöds inte |
| Indexåtgärder online | [Stöds](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [Stöds – storleksgräns för transaktioner per tjänstnivå](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| Operatorer | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | De flesta – se [enskilda operatorer](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) |
| Återställning till tidpunkt av databas | [Stöds](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [Stöds](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [Stöds](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Stöds inte
| Principbaserad hantering | [Stöds](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Stöds inte |
| Predikat | [Stöds](https://docs.microsoft.com/sql/t-sql/queries/predicates) | De flesta – se [enskilda predikat](https://docs.microsoft.com/sql/t-sql/queries/predicates)
| R-tjänster | [Stöds](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| Resursstyrning | [Stöds](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Stöds inte |
| RESTORE-uttryck | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Stöds inte | 
| Återställ databasen från en säkerhetskopia | [Stöds](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [Från endast inbyggda säkerhetskopior](sql-database-recovery-using-backups.md) |
| Säkerhet på radnivå | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| Semantisk sökning | [Stöds](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Stöds inte |
| Sekvensnummer | [Stöds](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [Stöds](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Service Broker | [Stöds](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Stöds inte |
| Inställningar för serverkonfiguration | [Stöds](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Finns inte stöd för – [Konfigurationsalternativ för databasen](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Ange uttryck | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | De flesta – se [enskilda uttryck](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 
| Spatial | [Stöds](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [Stöds](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| SQL Server Agent | [Stöds](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Finns inte stöd för – [elastiska jobb](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Stöds](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Finns inte stöd för – [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Stöds](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Finns inte stöd för – [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Stöds](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [Stöds](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server-profiler | [Stöds](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Finns inte stöd för – [utökade händelser](sql-database-xevent-db-diff-from-svr.md) |
| SQL Server-replikering | [Stöds](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Prenumerant för transaktions-och ögonblicksbildsreplikering endast](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Stöds](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Stöds inte |
| Lagrade procedurer | [Stöds](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [Stöds](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| Systemlagrade funktioner | [Stöds](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Vissa – se [enskilda funktioner](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) |
| Systemlagrade procedurer | [Stöds](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Vissa – se [enskilda lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Systemtabeller | [Stöds](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Vissa – se [enskilda tabeller](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Systemkatalogvyer | [Stöds](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Vissa – se [enskilda vyer](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)
| Tabellpartitionering | [Stöds](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Stöds – [endast primär filgrupp](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| Temporära tabeller | [Lokala och globala](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [Endast lokala](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| Temporala tabeller | [Stöds](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Stöds](sql-database-temporal-tables.md) |
| Transaktioner | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| Variabler | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [Stöds](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| Transparent datakryptering (TDE)  | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [Stöds](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Windows Server-redundansklustring | [Stöds](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Finns inte stöd för – [aktiv geo-replikering](sql-database-geo-replication-overview.md) |
| XML-index | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [Stöds](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- Mer information om stöd för och skillnader jämfört med Transact-SQL finns i [Azure SQL Database Transact-SQL skillnader](sql-database-transact-sql-information.md).

