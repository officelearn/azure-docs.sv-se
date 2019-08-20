---
title: Jämförelse av Azure SQL Database funktioner | Microsoft Docs
description: I den här artikeln jämförs funktionerna i SQL Server som är tillgängliga i olika varianter av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
ms.date: 05/10/2019
ms.openlocfilehash: c4ba2269003c9d401982b83f4e66c8caf45a0073
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624709"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Jämförelse av funktioner: Azure SQL Database jämfört med SQL Server

Azure SQL Database delar en gemensam kodbas med SQL Server. Vilka funktioner i SQL Server som stöds av Azure SQL Database beror på vilken typ av Azure SQL-databas som du skapar. Med Azure SQL Database kan du skapa en databas som en del av en [hanterad instans](sql-database-managed-instance.md), som en enskild databas eller som en del av en elastisk pool.

Microsoft fortsätter att lägga till funktioner i Azure SQL Database. Besök webb sidan för tjänst uppdateringar för Azure för de senaste uppdateringarna med följande filter:

- Filtrerade till [SQL Database-tjänsten](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrerade till allmän tillgänglighet [meddelanden (GA)](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) för SQL Database-funktioner.

Om du behöver mer information om skillnaderna kan du hitta dem på separata sidor för [en enkel databas och elastiska pooler](sql-database-transact-sql-information.md) eller [hanterade instanser](sql-database-managed-instance-transact-sql-information.md).

## <a name="sql-features"></a>SQL-funktioner

I följande tabell visas de viktigaste funktionerna i SQL Server och innehåller information om huruvida funktionen delvis eller helt stöds i hanterade instanser eller Enkel databas och elastiska pooler, med en länk till mer information om funktionen.

| **SQL-funktion** | **Enkla databaser och elastiska pooler** | **Hanterade instanser** |
| --- | --- | --- |
| [Alltid krypterad](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Ja – se [certifikat Arkiv](sql-database-always-encrypted.md) och [nyckel valv](sql-database-always-encrypted-azure-key-vault.md) | Ja – se [certifikat Arkiv](sql-database-always-encrypted.md) och [nyckel valv](sql-database-always-encrypted-azure-key-vault.md) |
| [Always on-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Haveri beredskap beskrivs i [Översikt över affärs kontinuitet med Azure SQL Database](sql-database-business-continuity.md) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas och [kan inte hanteras av användaren](sql-database-managed-instance-transact-sql-information.md#always-on-availability). Haveri beredskap beskrivs i [Översikt över affärs kontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [Koppla en databas](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nej | Nej |
| [Programroller](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Ja | Ja |
| [Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Ja](sql-database-auditing.md)| [Ja](sql-database-managed-instance-auditing.md), med vissa [skillnader](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [Automatisk säkerhets kopiering](sql-database-automated-backups.md) | Ja. Fullständiga säkerhets kopieringar görs var sjunde dag, Differentiellt 12 timmar och logg säkerhets kopior var 5-10: e minut. | Ja. Fullständiga säkerhets kopieringar görs var sjunde dag, Differentiellt 12 timmar och logg säkerhets kopior var 5-10: e minut. |
| [Automatisk justering (schema framtvingande)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)| [Ja](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatisk justering (index)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Ja](sql-database-automatic-tuning.md)| Nej |
| [Säkerhets kopierings kommando](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nej, endast systeminitierade automatiska säkerhets kopieringar – se [automatiserade säkerhets kopieringar](sql-database-automated-backups.md) | Ja, användaren initierade kopierings-endast säkerhets kopieringar till Azure Blob Storage (automatiska säkerhets kopieringar kan inte initieras av användaren) – se [skillnader i säkerhets kopiering](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Inbyggda funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta – se enskilda funktioner | Ja – se [lagrade procedurer, funktioner, utlösa skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) | 
| [BULK INSERTs instruktion](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | Ja, men precis från Azure Blob Storage som källa. | Ja, men precis från Azure Blob Storage som källa – se [skillnader](sql-database-managed-instance-transact-sql-information.md#bulk-insert--openrowset). |
| [Certifikat och asymmetriska nycklar](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | Ja, utan åtkomst till fil systemet för `BACKUP` och `CREATE` -åtgärder. | Ja, utan åtkomst till fil system för `BACKUP` och `CREATE` -åtgärder – se [certifikat skillnader](sql-database-managed-instance-transact-sql-information.md#certificates). | 
| [Registrering av ändrings data – CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nej | Ja |
| [Spårning av ändringar](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Ja |Ja |
| [Sortering – databas](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | Ja | Ja |
| [Sortering-Server/instans](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | Nej, standard sorteringen `SQL_Latin1_General_CP1_CI_AS` av logisk server används alltid. | Ja, kan ställas in när [instansen skapas](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) och kan inte uppdateras senare. |
| [Columnstore-index](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Ja – [Premium-nivå, standard nivå – S3 och över, generell användning nivå och affärskritisk nivåer](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Ja |
| [CLR (Common Language Runtime) – CLR](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nej | Ja, men utan åtkomst till fil systemet i `CREATE ASSEMBLY` instruktionen – se [CLR-skillnader](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Ja | Ja |
| [Inneslutna användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Ja | Ja |
| [Kontroll över nyckelord för flödes språk](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Ja | Ja |
| [Autentiseringsuppgifter](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | Ja, men endast [databasens begränsade autentiseringsuppgifter](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql). | Ja, men endast **Azure Key Vault** och `SHARED ACCESS SIGNATURE` stöds se [information](sql-database-managed-instance-transact-sql-information.md#credential) |
| [Namn frågor mellan databaser/tre delar](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Inga – se [elastiska frågor](sql-database-elastic-query-overview.md) | Ja, plus [elastiska frågor](sql-database-elastic-query-overview.md) |
| [Transaktioner över flera databaser](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nej | Ja, inom instansen. Se [skillnader i länkad server](sql-database-managed-instance-transact-sql-information.md#linked-servers) för frågor över olika instanser. |
| [Markörer](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Ja |Ja |
| [Data komprimering](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Ja |Ja |
| [Database mail – DbMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nej | Ja |
| [Databas spegling](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nej | [Nej](sql-database-managed-instance-transact-sql-information.md#database-mirroring) |
| [Konfigurations inställningar för databas](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Ja | Ja |
| [Databas ögonblicks bilder](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nej | Nej |
| [Datatyper](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Ja |Ja |
| [DBCC-instruktioner](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | De flesta – se enskilda uttryck | Ja – se [DBCC-skillnader](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL-instruktioner](https://docs.microsoft.com/sql/t-sql/statements/statements) | De flesta – se enskilda uttryck | Ja – se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [DDL-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Endast databas |  Ja |
| [Vyer för distribuerad partition](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Nej | Ja |
| [Distribuerade transaktioner-MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Inga – se [elastiska transaktioner](sql-database-elastic-transactions-overview.md) |  Inga se [skillnader mellan länkade servrar](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [DML-instruktioner](https://docs.microsoft.com/sql/t-sql/queries/queries) | Ja | Ja |
| [DML-utlösare](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | De flesta – se enskilda uttryck |  Ja |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | De flesta – se enskilda DMV: er |  Ja – se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Ja](sql-database-dynamic-data-masking-get-started.md)| [Ja](sql-database-dynamic-data-masking-get-started.md) |
| [Händelseaviseringar](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Inga-se [aviseringar](sql-database-insights-alerts-portal.md) | Nej |
| [Uttryck](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Ja | Ja |
| [Utökade händelser (XEvent)](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Några-se [utökade händelser i SQL Database](sql-database-xevent-db-diff-from-svr.md) | Ja – Visa [skillnader i utökade händelser](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nej | Nej |
| [Filer och fil grupper](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Endast primär fil grupp | Ja. Fil Sök vägar tilldelas automatiskt och fil platsen kan inte anges i `ALTER DATABASE ADD FILE` [instruktionen](sql-database-managed-instance-transact-sql-information.md#alter-database-statement).  |
| [-](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nej | [Nej](sql-database-managed-instance-transact-sql-information.md#filestream-and-filetable) |
| [Full texts ökning (FT: er)](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Ja, men ord separatorer från tredje part stöds inte | Ja, men [ord separatorer från tredje part stöds inte](sql-database-managed-instance-transact-sql-information.md#full-text-semantic-search) |
| [Funktioner](https://docs.microsoft.com/sql/t-sql/functions/functions) | De flesta – se enskilda funktioner | Ja – se [lagrade procedurer, funktioner, utlösa skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [Diagram bearbetning](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Ja | Ja |
| [Minnesintern optimering](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Ja – [Premium-och affärskritisk nivåer endast](sql-database-in-memory.md) begränsat stöd för icke-beständiga minnes objekt, till exempel tabell typer | Ja – [endast affärskritisk nivå](sql-database-managed-instance.md) |
| [Stöd för JSON-data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Ja](sql-database-json-features.md) | [Ja](sql-database-json-features.md) |
| [Språk element](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | De flesta – se enskilda element |  Ja – se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Länkade servrar](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Ingen-se [elastisk fråga](sql-database-elastic-query-horizontal-partitioning.md) | Ja. Endast till [SQL Server och SQL Database](sql-database-managed-instance-transact-sql-information.md#linked-servers) utan distribuerade transaktioner. |
| [Länkade servrar](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) som läser från filer (CSV, Excel)| Nej. Använd [bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) eller [OpenRowSet](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) som ett alternativ för CSV-format. | Nej. Använd [bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) eller [OpenRowSet](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) som ett alternativ för CSV-format. Spåra dessa förfrågningar på [feedback-objektet för hanterade instanser](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|
| [Logg överföring](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Haveri beredskap beskrivs i [Översikt över affärs kontinuitet med Azure SQL Database](sql-database-business-continuity.md) | Inbyggt inbyggt som en del av processen för DMS-migrering. Inte tillgängligt som lösning för hög tillgänglighet, eftersom andra metoder för [hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas och vi inte rekommenderas att använda log-transport som ett alternativ. Haveri beredskap beskrivs i [Översikt över affärs kontinuitet med Azure SQL Database](sql-database-business-continuity.md). Inte tillgänglig som en mekanism för replikering mellan databaser – Använd sekundära repliker på [affärskritisk nivå](sql-database-service-tier-business-critical.md), [grupper för automatisk redundans](sql-database-auto-failover-group.md)eller [transaktionell replikering](sql-database-managed-instance-transactional-replication.md) som alternativ. |
| [Inloggningar och användare](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | Ja, men `CREATE` och `ALTER` inloggnings instruktioner erbjuder inte alla alternativ (inga Windows-och server nivå Azure Active Directory inloggningar). `EXECUTE AS LOGIN`stöds inte-Använd `EXECUTE AS USER` i stället.  | Ja, med vissa [skillnader](sql-database-managed-instance-transact-sql-information.md#logins-and-users). Windows-inloggningar stöds inte och de bör ersättas med Azure Active Directory inloggningar. |
| [Minimal loggning i Mass import](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nej, endast fullständig återställnings modell stöds. | Nej, endast fullständig återställnings modell stöds. |
| [Ändra system data](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nej | Ja |
| [OLE-automation](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | Nej | Nej |
| [Online index åtgärder](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Ja | Ja |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Nej|Ja, bara för andra Azure SQL-databaser och SQL-servrar. Se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Ja|Ja|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Nej|Ja, bara för andra Azure SQL-databaser och SQL-servrar. Se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Ja, endast för import från Azure Blob Storage. |Ja, bara för andra Azure SQL-databaser och SQL-servrar och för att importera från Azure Blob Storage. Se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Ja|Ja|
| [Operatörer](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | De flesta – se enskilda operatörer |Ja – se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Partitionering](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Ja | Ja |
| Offentlig IP-adress | Ja. Åtkomsten kan begränsas med hjälp av brand Väggs-eller tjänst slut punkter.  | Ja. Måste aktive ras explicit och port 3342 måste vara aktive rad i NSG-regler. Offentliga IP-adresser kan inaktive ras om det behövs. Mer information finns i den [offentliga slut punkten](sql-database-managed-instance-public-endpoint-securely.md) . | 
| [Återställning av tidpunkt för databas](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Ja – alla andra tjänst nivåer än storskalig-se [SQL Database återställning](sql-database-recovery-using-backups.md#point-in-time-restore) | Ja – se [SQL Database återställning](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nej. Du kan fråga efter data i filerna som placeras på Azure Blob Storage `OPENROWSET` med hjälp av funktionen. | Nej. Du kan fråga efter data i filerna som placeras på Azure Blob Storage `OPENROWSET` med hjälp av funktionen. |
| [Predikat](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Ja | Ja |
| [Fråga om aviseringar](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | Nej | Ja |
| [R-tjänster](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Ja, i [offentlig för hands version](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Nej |
| [Återställnings modeller](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | Endast fullständig återställning som garanterar hög tillgänglighet stöds. Enkla och Mass återställnings modeller är inte tillgängliga. | Endast fullständig återställning som garanterar hög tillgänglighet stöds. Enkla och Mass återställnings modeller är inte tillgängliga. | 
| [Resurs styrning](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nej | Ja |
| [Restore-instruktioner](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nej | Ja, med obligatoriska `FROM URL` alternativ för de säkerhets kopierings filer som placerats på Azure Blob Storage. Se [återställnings skillnader](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Återställ databasen från en säkerhets kopia](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Från automatiska säkerhets kopieringar – se [SQL Database återställning](sql-database-recovery-using-backups.md) | Från automatiska säkerhets kopieringar – se [SQL Database återställning](sql-database-recovery-using-backups.md) och fullständiga säkerhets kopior som finns på Azure Blob Storage – se [skillnader i säkerhets kopiering](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Återställ databasen till SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Nej. Använd BACPAC eller BCP i stället för intern återställning. | Nej, eftersom SQL Server databas motor som används i hanterade instanser har högre version än en RTM-version av SQL Server som används lokalt. Använd BACPAC, BCP eller transaktionell replikering i stället. |
| [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Ja | Ja |
| [Semantisk sökning](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nej | Nej |
| [Serie nummer](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Ja | Ja |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nej | Ja, men bara inom instansen. Se [Service Broker skillnader](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Server konfigurations inställningar](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nej | Ja – se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Set-instruktioner](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | De flesta – se enskilda uttryck | Ja – se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [Rumslig](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Ja | Ja |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Inga – se [elastiska jobb](elastic-jobs-overview.md) | Ja – se [SQL Server Agent skillnader](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | No-se [SQL Database granskning](sql-database-auditing.md) | Ja – se [skillnader i granskning](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Ja | Ja |
| [System lagrade funktioner](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | De flesta – se enskilda funktioner | Ja – se [lagrade procedurer, funktioner, utlösa skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [System lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Några – se enskilda lagrade procedurer | Ja – se [lagrade procedurer, funktioner, utlösa skillnader](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [System tabeller](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Vissa – se enskilda tabeller | Ja – se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Vyer för system katalog](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Vissa – se enskilda vyer | Ja – se [skillnader i T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database) | Ja. [32 GB storlek per kärna för varje databas](sql-database-vcore-resource-limits-single-databases.md). | Ja. [24GB-storlek per vCore för hela GP-nivån och begränsad av instans storleken på BC-nivå](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)  |
| [Temporary tables](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) (Temporära tabeller) | Lokala och databas omfattningar globala temporära tabeller | Lokala och instans omfattningar globala temporära tabeller |
| [Temporala tabeller](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Ja](sql-database-temporal-tables.md) | [Ja](sql-database-temporal-tables.md) |
| Val av tidszon | Nej | [Ja](sql-database-managed-instance-timezone.md), och det måste konfigureras när den hanterade instansen skapas. |
| Identifiering av hot|  [Ja](sql-database-threat-detection.md)|[Ja](sql-database-managed-instance-threat-detection.md)|
| [Spårnings flaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Nej | Ja, men endast en begränsad uppsättning globala spårnings flaggor. Se [DBCC-skillnader](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md) | Ja, [endast transaktionell prenumerant och replikering av ögonblicks bilder](sql-database-single-database-migrate.md) | Ja, i [offentlig för hands version](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance). Se begränsningarna [här](sql-database-managed-instance-transact-sql-information.md#replication). |
| [Variabler](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Ja | Ja |
| [Transparent datakryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Ja – endast Generell användning och Affärskritisk tjänst nivåer| [Ja](transparent-data-encryption-azure-sql.md) |
| [Kluster för växling vid fel i Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Nej. Andra metoder som ger [hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Haveri beredskap beskrivs i [Översikt över affärs kontinuitet med Azure SQL Database](sql-database-business-continuity.md) | Nej. Andra metoder som ger [hög tillgänglighet](sql-database-high-availability.md) ingår i varje databas. Haveri beredskap beskrivs i [Översikt över affärs kontinuitet med Azure SQL Database](sql-database-business-continuity.md) |
| [XML-index](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Ja | Ja |

## <a name="platform-capabilities"></a>Plattformsfunktioner

Azure-plattformen tillhandahåller ett antal PaaS-funktioner som läggs till som ett extra värde för standard databas funktionerna. Det finns ett antal externa tjänster som kan användas med Azure SQL Database-tjänsten. 

| **Plattforms funktion** | **Enkla databaser och elastiska pooler** | **Hanterade instanser** |
| --- | --- | --- |
| [Aktiv geo-replikering](sql-database-active-geo-replication.md) | Ja – alla tjänst nivåer förutom storskalig | Nej, se [grupperna för automatisk redundans (förhands granskning)](sql-database-auto-failover-group.md) som ett alternativ |
| [Automatiska redundansgrupper](sql-database-auto-failover-group.md) | Ja – alla tjänst nivåer förutom storskalig | Ja, i [offentlig för hands version](sql-database-auto-failover-group.md)|
| [Azure Resource Health](/azure/service-health/resource-health-overview) | Ja | Nej |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Ja | Ja |
| Fil system åtkomst | Nej. Använd [bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) eller [OpenRowSet](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) för att komma åt och läsa in data från Azure Blob Storage som ett alternativ. | Nej. Använd [bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) eller [OpenRowSet](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) för att komma åt och läsa in data från Azure Blob Storage som ett alternativ. |
| [Geo-återställning](sql-database-recovery-using-backups.md#geo-restore) | Ja – alla tjänst nivåer förutom storskalig | Ja – använder [Azure PowerShell](https://medium.com/azure-sqldb-managed-instance/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa). |
| [Skalnings arkitektur](sql-database-service-tier-hyperscale.md) | Ja | Nej |
| [Långsiktig kvarhållning av säkerhets kopior – LTR](sql-database-long-term-retention.md) | Ja, Behåll automatiskt säkerhets kopieringar upp till 10 år. | Inte ännu. Använd `COPY_ONLY` [manuella säkerhets kopieringar](sql-database-managed-instance-transact-sql-information.md#backup) som en tillfällig lösning. |
| [Principbaserad hantering](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nej | Nej |
| Resurspooler | Ja, som [elastiska pooler](sql-database-elastic-pool.md) | Den inbyggda-en enda hanterade instansen kan ha flera databaser som delar samma resurspool |
| Skala upp eller ned (online) | Ja, du kan antingen ändra DTU eller reserverad virtuella kärnor eller maximum Storage med minimal stillestånds tid. | Ja, du kan ändra reserverad virtuella kärnor eller maximum Storage med minimal stillestånds tid. | 
| Autoskala | Ja, i [Server lös modell](sql-database-serverless.md) | Nej, du måste välja reserverad beräkning och lagring. |
| Pausa/återuppta | Ja, i [Server lös modell](sql-database-serverless.md) | Nej | 
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [Ja](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | Ja, [version 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SQL-analys](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | Ja | Ja |
| [SQL Data Sync](sql-database-get-started-sql-data-sync.md) | Ja | Nej |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Ja | Ja |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Nej, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) är en separat Azure-moln tjänst. | Nej, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) är en separat Azure-moln tjänst. |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Ja, med en hanterad SSIS i Azure Data Factory (ADF)-miljö, där paket lagras i SSISDB som hanteras av Azure SQL Database och körs på Azure SSIS Integration Runtime (IR), se [Skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Information om hur du jämför SSIS-funktionerna i SQL Database Server och en hanterad instans finns i [jämför Azure SQL Database enstaka databaser/elastiska pooler och hanterade instanser](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). | Ja, med en hanterad SSIS i Azure Data Factory (ADF)-miljö, där paket lagras i SSISDB som hanteras av hanterade instanser och körs på Azure SSIS Integration Runtime (IR), se [Skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Om du vill jämföra SSIS-funktionerna i SQL Database och hanterade instanser, se [jämför Azure SQL Database enstaka databaser/elastiska pooler och hanterade instanser](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | No- [se Power BI](https://docs.microsoft.com/power-bi/) | No- [se Power BI](https://docs.microsoft.com/power-bi/) |
| [Fråga prestanda insikter (QPI)](sql-database-query-performance.md) | Ja | Nej. Använd inbyggda rapporter i SQL Server Management Studio och Azure Data Studio. |
| [VNet](../virtual-network/virtual-networks-overview.md) | Delvis, den ger begränsad åtkomst med [VNet](sql-database-vnet-service-endpoint-rule-overview.md) -slutpunkter | Ja, den hanterade instansen matas in i kundens VNet. Se [undernät](sql-database-managed-instance-transact-sql-information.md#subnet) och [VNet](sql-database-managed-instance-transact-sql-information.md#vnet) |

## <a name="tools"></a>Verktyg
Azure SQL Database har stöd för olika data verktyg som kan hjälpa dig att hantera dina data.

| **SQL-verktyg** | **Enkla databaser och elastiska pooler** | **Hanterade instanser** |
| --- | --- | --- |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | Ja | Ja |
| [BACPAC-fil (export)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ja – se [SQL Database export](sql-database-export.md) | Ja – se [SQL Database export](sql-database-export.md) |
| [BACPAC-fil (importera)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ja – se [SQL Database import](sql-database-import.md) | Ja – se [SQL Database import](sql-database-import.md) |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nej | Nej |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nej | Nej |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Ja | Ja |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Ja | Ja [version 18,0 och högre](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Inga – se [utökade händelser](sql-database-xevent-db-diff-from-svr.md) | Ja |
| [System Center Operations Manager-SCOM](https://docs.microsoft.com/system-center/scom/welcome) | [Ja](https://www.microsoft.com/download/details.aspx?id=38829) | Nej |

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- Information om en hanterad instans finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
