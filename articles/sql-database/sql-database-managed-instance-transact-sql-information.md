---
title: Azure SQL Database hanteras instans T-SQL skillnader | Microsoft Docs
description: Den här artikeln beskrivs skillnaderna mellan hanterade Azure SQL Database-instans och SQL Server T-SQL.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/19/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: b633c3c4a4f476cb8e89afde8adeb94558643d4b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database hanteras instans T-SQL-skillnader från SQL Server 

Azure SQL-hanterade databasinstans (förhandsversion) ger hög kompatibilitet med lokal SQL Server Database Engine. De flesta av SQL Server Database Engine-funktioner stöds i hanterade instans. Eftersom det finns fortfarande vissa skillnader i syntaxen och beteende, den här artikeln sammanfattar och förklarar skillnaderna.
 - [Skillnader i T-SQL och funktioner som inte stöds](#Differences)
 - [Funktioner som har olika beteenden i hanterade instans](#Changes)
 - [Tillfällig begränsningar och kända problem](#Issues)

## <a name="Differences"></a> T-SQL-skillnader från SQL Server 

Det här avsnittet beskrivs viktiga skillnader i T-SQL-syntax och beteende mellan hanterade instans och lokala SQL Server Database Engine, samt funktioner som inte stöds.

### <a name="always-on-availability"></a>Finns alltid i tillgänglighet

[Hög tillgänglighet](sql-database-high-availability.md) är inbyggd i hanterade instansen och inte kan styras av användare. Följande uttryck stöds inte:
 - [SKAPA SLUTPUNKT... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
 - [SKAPA TILLGÄNGLIGHETSGRUPPEN](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
 - [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
 - [TA BORT TILLGÄNGLIGHETSGRUPPEN](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
 - [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) -satsen i den [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) instruktionen

### <a name="auditing"></a>Granskning 
 
De viktigaste skillnaderna mellan SQL-granskning i hanterade instans, Azure SQL Database och SQL Server lokalt är:
- I hanteras-instansen SQL Audit fungerar på servernivå och lagrar `.xel` filer på Azure blob storage-konto.  
- I Azure SQL Database fungerar SQL Audit på databasnivå.
- I SQL Server lokalt / virtuella SQL-granskning fungerar på servernivå, men lagrar händelser på filer system och windows-händelseloggar.  
  
XEvent granskning i hanterade instans stöder Azure blob storage-mål. Fil- och windows-loggar stöds inte.    
 
Nyckeln skillnader i den `CREATE AUDIT` syntaxen för granskning till Azure blob storage är:
- Ny syntax `TO URL` tillhandahålls och låter dig ange Webbadressen till Azure blob Storage-behållare där `.xel` filer ska placeras 
- Syntaxen `TO FILE` stöds inte eftersom hanteras instansen inte kan komma åt filresurser för Windows. 
 
Mer information finns i:  
- [SKAPA SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Backup 

Hanterade instansen har automatisk säkerhetskopiering och kan du skapa fullständiga databas `COPY_ONLY` säkerhetskopior. Differentiella, logg och säkerhetskopior av filögonblicksbilder stöds inte.  
- Hanterade instans kan säkerhetskopiera en databas bara till ett Azure Blob Storage-konto: 
 - Endast `BACKUP TO URL` stöds 
 - `FILE`, `TAPE`, och enheter för säkerhetskopiering stöds inte  
- De flesta av allmänna `WITH` alternativ stöds 
 - `COPY_ONLY` är obligatoriskt
 - `FILE_SNAPSHOT` Stöds inte  
 - Bandalternativ: `REWIND`, `NOREWIND`, `UNLOAD`, och `NOUNLOAD` stöds inte 
 - Log-specifika alternativ: `NORECOVERY`, `STANDBY`, och `NO_TRUNCATE` stöds inte 
 
Begränsningar:  
- Hanterade instans kan säkerhetskopiera en databas till en säkerhetskopia med upp till 32 stripe som är tillräckligt för databaserna upp till 4 TB om säkerhetskopieringskomprimering används.
- Maxstorlek för säkerhetskopiering stripe är 195 GB (högsta blob storlek). Öka antalet stripe i kommandot backup för att minska Stripestorleken för enskilda och hålla sig inom den här gränsen. 

> [!TIP]
> Att undvika den här begränsningen lokalt, säkerhetskopiering till `DISK` i stället för säkerhetskopiering till `URL`, ladda upp säkerhetskopian för att blob och sedan återställa. Återställ filer som stöder större eftersom en annan blob-typ används.  

Information om säkerhetskopiering med T-SQL finns [säkerhetskopiering](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

### <a name="buffer-pool-extension"></a>Buffertpooltillägget 
 
- [Bufferten poolen tillägget](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) stöds inte.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` stöds inte. Se [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql). 
 
### <a name="bulk-insert--openrowset"></a>Massinfogning / openrowset

Hanterade instansen kan inte komma åt delade filer och mappar för Windows, så att filerna måste importeras från Azure blob storage.
- `DATASOURCE` krävs i `BULK INSERT` kommandot när du importerar filer från Azure blob storage. Se [MASSINFOGNING](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` krävs i `OPENROWSET` fungera när du läser en innehållet i en fil från Azure blob storage. Se [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
 
### <a name="certificates"></a>Certifikat 

Hanterade instansen kan inte komma åt delade filer och mappar för Windows, så gäller följande begränsningar: 
- `CREATE FROM`/`BACKUP TO` filen finns inte stöd för certifikat
- `CREATE`/`BACKUP` certifikat från `FILE` / `ASSEMBLY` stöds inte. Privata nyckelfilerna kan inte användas.  
 
Se [skapa certifikat](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) och [säkerhetskopiera certifikat](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
> [!TIP]
> Lösning: script certifikat och privata nycklar, lagra som SQL-filen och skapa från binärfil: 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

Hanterade instansen kan inte komma åt delade filer och mappar för Windows, så gäller följande begränsningar: 
- Endast `CREATE ASSEMBLY FROM BINARY` stöds. Se [skapa sammansättningen från binär](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` stöds inte. Se [skapa sammansättningen från filen](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` Det går inte att referera till filer. Se [ändring av sammansättningen](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).
 
### <a name="compatibility-levels"></a>Efterlevnadsnivåer 
 
- Stöds kompatibilitetsnivåer är: 100, 110, 120, 130, 140  
- Kompatibilitetsnivåer under 100 stöds inte. 
- Kompatibilitetsnivån standard för nya databaser är 140. För återställda databaser påverkas kompatibilitetsnivå inte om den har 100 och senare.

Se [ALTER DATABASENS kompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
 
### <a name="credential"></a>Autentiseringsuppgift 
 
Endast Azure Key Vault och `SHARED ACCESS SIGNATURE` identiteter stöds. Windows-användare stöds inte.
 
Se [skapa AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) och [ALTER AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql). 
 
### <a name="cryptographic-providers"></a>Kryptografiproviders

Hanterade instansen kan inte komma åt filer så kryptografiproviders inte kan skapas:
- `CREATE CRYPTOGRAPHIC PROVIDER` stöds inte. Se [skapa KRYPTOGRAFIPROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` stöds inte. Se [ALTER KRYPTOGRAFIPROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql). 

### <a name="collation"></a>Sortering 
 
Serversorteringen är `SQL_Latin1_General_CP1_CI_AS` och kan inte ändras. Se [sorteringar](https://docs.microsoft.com/sql/t-sql/statements/collations).
 
### <a name="database-options"></a>Databasalternativ 
 
- Flera loggfiler stöds inte. 
- InMemory-objekt stöds inte i generella tjänstnivå.  
- Det finns en gräns på 280 filer per instans innebär max 280 filer per databas. Både data och loggfiler räknas mot den här gränsen.  
- Databasen får inte innehålla filgrupper som innehåller filestream-data.  Återställningen misslyckas om .bak innehåller `FILESTREAM` data.  
- Varje fil placeras i Azure Premium-lagring. I/o och genomströmning per fil beror på storleken på varje enskild fil på samma sätt som de gör för Azure Premium Storage diskar. Se [Azure Premium diskprestanda](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)  
 
#### <a name="create-database-statement"></a>CREATE DATABASE-instruktionen

Följande är `CREATE DATABASE` begränsningar: 
- Filer och filgrupper kan inte definieras.  
- `CONTAINMENT` alternativet stöds inte.  
- `WITH`alternativ stöds inte.  
   > [!TIP]
   > Som tillfällig lösning kan du använda `ALTER DATABASE` när `CREATE DATABASE` ange databasalternativ att lägga till filer eller ange inneslutning.  

- `FOR ATTACH` alternativet stöds inte 
- `AS SNAPSHOT OF` alternativet stöds inte 

Mer information finns i [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ALTER DATABASE-instruktionen

Vissa egenskaper kan inte ange eller ändra:
- Sökvägen kan inte anges i `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-instruktionen. Ta bort `FILENAME` från skriptet eftersom instansen hanteras automatiskt placerar filerna.  
- Filnamnet kan inte ändras med hjälp av `ALTER DATABASE` instruktionen.

Följande alternativ anges som standard och kan inte ändras: 
- `MULTI_USER` 
- `ENABLE_BROKER ON` 
- `AUTO_CLOSE OFF` 

Det går inte att ändra följande alternativ: 
- `AUTO_CLOSE` 
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)` 
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)` 
- `DISABLE_BROKER` 
- `EMERGENCY` 
- `ENABLE_BROKER` 
- `FILESTREAM` 
- `HADR`   
- `NEW_BROKER` 
- `OFFLINE` 
- `PAGE_VERIFY` 
- `PARTNER` 
- `READ_ONLY` 
- `RECOVERY BULK_LOGGED` 
- `RECOVERY_SIMPLE` 
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER` 
- `SINGLE_USER` 
- `WITNESS`

Ändra namn stöds inte.

Mer information finns i [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="database-mirroring"></a>Databasspegling

Databasspegling stöds inte.
 - `ALTER DATABASE SET PARTNER` och `SET WITNESS` alternativ stöds inte.
 - `CREATE ENDPOINT … FOR DATABASE_MIRRORING` stöds inte.

Mer information finns i [ALTER DATABASE SET PARTNER och SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) och [CREATE ENDPOINT... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="dbcc"></a>DBCC 
 
Odokumenterade DBCC-instruktioner som är aktiverade i SQL Server stöds inte i hanterade instans.
- `Trace Flags` stöds inte. Se [Spårningsflaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` stöds inte. Se [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` stöds inte. Se [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="extended-events"></a>Extended Events 

Vissa Windows-specifika mål för XEvents stöds inte:
- `etw_classic_sync target` stöds inte. Store `.xel` filer på Azure blob storage. Se [etw_classic_sync mål](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target). 
- `event_file target`stöds inte. Store `.xel` filer på Azure blob storage. Se [event_file mål](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target).

### <a name="external-libraries"></a>Externt bibliotek

I-databasen R och Python externt bibliotek inte stöds ännu. Se [SQL Server-Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream och Filetable

- FileStream-data stöds inte. 
- Databasen får inte innehålla filgrupper med `FILESTREAM` data
- `FILETABLE` stöds inte
- Tabeller kan inte ha `FILESTREAM` typer
- Följande funktioner stöds inte:
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

Mer information finns i [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) och [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Fulltextsökning semantiska

[Semantiska Sök](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) stöds inte.

### <a name="linked-servers"></a>Länkade servrar
 
Länkade servrar i hanterade instans stöder begränsat antal mål: 
- Mål som stöds: SQLServer, SQL-databas, hanterade-instans och SQL Server på en virtuell dator.
- Mål som inte stöds: filer, Analysis Services och andra RDBMS.

Åtgärder

- Cross-instans skrivtransaktioner stöds inte.
- `sp_dropserver` Det finns stöd för att släppa en länkad server. Se [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` funktionen kan användas för att köra frågor bara på SQL Server-instanser (antingen hanteras lokalt, eller på virtuella datorer). Se [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` funktionen kan användas för att köra frågor bara på SQL Server-instanser (antingen hanteras lokalt, eller på virtuella datorer). Endast `SQLNCLI`, `SQLNCLI11`, och `SQLOLEDB` värden stöds som provider. Till exempel: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Se [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
 
### <a name="logins--users"></a>Inloggningar / användare 

- SQL-inloggningar som skapats `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, och `FROM SID` stöds. Se [skapa inloggningen](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Windows-inloggningar som skapats med `CREATE LOGIN ... FROM WINDOWS` syntax stöds inte.
- Azure Active Directory (Azure AD)-användaren som skapade instansen har [obegränsad administratörsrättigheter](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts).
- Icke-administratörer Azure Active Directory (AD Azure) på databasnivå användare kan skapas med `CREATE USER ... FROM EXTERNAL PROVIDER` syntax. Se [skapa användare... FRÅN EXTERN PROVIDER](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)
 
### <a name="polybase"></a>Polybase

Externa tabeller som refererar till filer i HDFS eller Azure blob storage stöds inte. Information om Polybase finns [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikering 
 
Replikering stöds inte ännu. Information om replikering finns i [SQL Server Replication](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication).
 
### <a name="restore-statement"></a>ÅTERSTÄLLA instruktionen 
 
- Stöds syntax  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- Syntax som inte stöds 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- Källa  
 - `FROM URL` (Azure blob storage) är bara alternativ som stöds.
 - `FROM DISK`/`TAPE`/ enhet för säkerhetskopiering stöds inte.
 - Säkerhetskopior stöds inte. 
- `WITH` alternativen kan inte användas (ingen `DIFFERENTIAL`, `STATS`osv.)     
- `ASYNC RESTORE` -Återställning fortsätter även om klientanslutningen bryts. Om anslutningen bryts, kan du kontrollera `sys.dm_operation_status` visa status för en återställning (samt skapa och ta bort databasen). Se [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  
 
Följande databasalternativ för är uppsättningen/åsidosätts och kan inte ändras senare:  
- `NEW_BROKER` (om broker inte är aktiverat i bak-filen)  
- `ENABLE_BROKER` (om broker inte är aktiverat i bak-filen)  
- `AUTO_CLOSE=OFF` (om en databas i bak-filen har `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (om en databas i bak-filen har `SIMPLE` eller `BULK_LOGGED` återställningsläge)
- Minnesoptimerade filgruppen läggs och kallas XTP om den inte har .bak källfilen  
- Alla befintliga minnesoptimerade filgruppen har bytt namn till XTP  
- `SINGLE_USER` och `RESTRICTED_USER` alternativ konverteras till `MULTI_USER`   
Begränsningar:  
- `.BAK` filer som innehåller flera säkerhetskopior kan inte återställas. 
- `.BAK` filer som innehåller flera loggfiler kan inte återställas. 
- Återställningen misslyckas om .bak innehåller `FILESTREAM` data.
- Säkerhetskopieringar som innehåller databaser som har aktiva InMemory-objekt kan inte återställas för tillfället.  
- Säkerhetskopieringar som innehåller databaser där på någon punkt i minnesobjekt fanns kan för närvarande inte återställas.   
- Säkerhetskopieringar som innehåller databaser i skrivskyddat läge kan inte återställas för tillfället. Den här begränsningen tas bort snart.   
 
Information om återställning uttrycken, se [ÅTERSTÄLLA instruktioner](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker 
 
- Cross-instans service broker stöds inte 
 - `sys.routes` -Förutsättning: Välj adress sys.routes. Adressen måste vara lokal för varje flöde. Se [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
 - `CREATE ROUTE` -Det går inte att `CREATE ROUTE` med `ADDRESS` än `LOCAL`. Se [skapa väg](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
 - `ALTER ROUTE` Det går inte att `ALTER ROUTE` med `ADDRESS` än `LOCAL`. Se [ALTER väg](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  
 
### <a name="service-key-and-service-master-key"></a>Tjänsten huvudnyckeln för tjänsten och de 
 
- [Huvudnyckeln säkerhetskopiering](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) stöds inte (hanteras av tjänsten SQL-databas) 
- [Återställ huvudnyckeln](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) stöds inte (hanteras av tjänsten SQL-databas) 
- [Tjänstens huvudnyckel säkerhetskopiering](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) stöds inte (hanteras av tjänsten SQL-databas) 
- [Tjänstens huvudnyckel återställning](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) stöds inte (hanteras av tjänsten SQL-databas) 
 
### <a name="stored-procedures-functions-triggers"></a>Lagrade procedurer, funktioner, utlösare 
 
- `NATIVE_COMPILATION` för närvarande stöds inte. 
- Följande [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) alternativ stöds inte: 
 - `allow polybase export` 
 - `allow updates` 
 - `filestream_access_level` 
 - `max text repl size` 
 - `remote data archive` 
 - `remote proc trans` 
- `sp_execute_external_scripts` stöds inte. Se [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` stöds inte. Se [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` stöds inte, inklusive `sp_addextendedproc` och `sp_dropextendedproc`. Se [utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, och `sp_detach_db` stöds inte. Se [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), och [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- `sp_renamedb` stöds inte. Se [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

### <a name="sql-server-agent"></a>SQL Server Agent 
 
- Inställningar för SQL Agent är skrivskyddade. Proceduren `sp_set_agent_properties` stöds inte i hanterade instans.  
- Jobb - bara stegen för T-SQL-jobbet är för närvarande stöds (fler steg som ska läggas till under förhandsversion).
 - SSIS stöds inte ännu. 
 - Replikering stöds inte ännu  
  - Transaktionsloggen läsaren stöds inte ännu.  
  - Ögonblicksbilder stöds inte ännu.  
  - Distributören stöds inte ännu.  
  - Koppling stöds inte.  
  - Kön Reader stöds inte.  
 - Kommandogränssnittet stöds inte ännu. 
  - Hanterade instansen kan inte komma åt externa resurser (till exempel nätverksresurser via robocopy).  
 - PowerShell stöds inte ännu.
 - Analysis Services stöds inte.  
- Meddelanden stöds delvis.
 - E-postavisering stöds, måste du konfigurera en profil för Database Mail. Det kan finnas en enda database mail-profilen och måste anropas `AzureManagedInstance_dbmail_profile` som förhandsversion (tillfälliga begränsningen).  
 - Personsökare stöds inte.  
 - NetSend stöds inte. 
 - Aviseringar har ännu stöds inte inte.
 - Proxyservrar stöds inte.  
- Händelseloggen stöds inte. 
 
Följande funktioner stöds inte för närvarande men aktiveras i framtiden:  
- Proxyservrar
- Schemalägger jobb på inaktiv CPU 
- Aktivering/inaktivering av Agent
- Aviseringar

Information om SQL Server Agent finns [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).
 
### <a name="tables"></a>Tabeller 

Följande stöds inte: 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

Information om att skapa och ändra tabeller finns [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) och [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).
 
## <a name="Changes"></a> Funktionsändringar 
 
Returnerar olika resultat följande variabler, uppgifter och vyer:  
- `SERVERPROPERTY('EngineEdition')` Returnerar värdet 8. Den här egenskapen identifierar hanteras instans. Se [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Returnerar kort instansnamnet, till exempel 'minserver'. Se [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Returnerar fullständig DNS 'anslutningsbart' namn, till exempel Mina-hanterad-instance.wcus17662feb9ce98.database.windows.net. Se [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -Returnerar fullständig 'anslutningsbart' DNS-namn som `myinstance.domain.database.windows.net` för egenskaper 'name' och 'data_source'. Se [SYS. SERVRAR](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVERNAME` Returnerar fullständig 'anslutningsbart' DNS-namn som `my-managed-instance.wcus17662feb9ce98.database.windows.net`. Se [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -Returnerar fullständig 'anslutningsbart' DNS-namn som `myinstance.domain.database.windows.net` för egenskaper 'name' och 'data_source'. Se [SYS. SERVRAR](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVICENAME` Returnerar NULL, som det inget logiskt i hanterade instans miljö. Se [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).   
- `SUSER_ID` stöds. Returnerar NULL om AAD-inloggningen inte är i sys.syslogins. Se [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` stöds inte. Returnerar felaktiga data (tillfälligt kända problem). Se [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 
- `GETDATE()` och andra inbyggda datum/tid-funktioner returnerar alltid tid i UTC-tid. Se [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Kända problem och begränsningar

### <a name="tempdb-size"></a>TEMPDB storlek

`tempdb` är uppdelat i 12 filer med max storlek på 14 GB per fil. Det går inte att ändra den här största storleken per fil och nya filer kan inte läggas till `tempdb`. Den här begränsningen tas bort snart. Några frågor kan returnera ett fel om de behöver mer än 168 GB i `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Överstiger lagringsutrymme med små databasfiler

Varje hanteras instans har till 35 TB lagringsutrymme som är reserverade för Azure Premium diskutrymme och varje databasfil placeras på en separat fysisk disk. Diskstorlekar kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Debiteras inte outnyttjat utrymme på disken, men den totala summan av Azure Premium diskstorlekar får inte överskrida 35 TB. I vissa fall kan innebära en hanterad instanstjänsten som inte behöver 8 TB totalt 35 TB Azure gränsen lagringsstorleken på grund av interna fragmentering. 

En hanterad instans kan till exempel ha en fil med 1,2 TB storlek som använder en disk med 4 TB och 248 filer med 1 GB varje som placeras på 248 diskar med 128 GB storlek. I det här exemplet är den totala lagringsstorleken 1 x 4 TB + 248 x 128 GB = 35 TB. Totalt antal reserverade instansstorleken för databaser är dock 1 x 1.2 TB + 248 x 1 GB = 1,4 TB. Detta visar att under vissa omständigheter på grund av en särskild fördelning av filer, en hanterad instans kan nå lagringsgränsen för Azure Premium-Disk där du inte kan förväntar. 

Det är något fel på befintliga databaser och de kan växa utan problem om nya filer läggs inte, men nya databaser kan inte skapas eller återställas eftersom det inte finns tillräckligt med utrymme för nya diskenheter används, även om den totala storleken på alla databaser inte når t han instansen storleksgränsen. Fel som returneras är i så fall inte klar.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Felaktig konfiguration av SAS-nyckel under databasen återställa

`RESTORE DATABASE` som läser bak-filen kan ständigt försöka läsa bak-filen och returnerar fel efter lång tid om signatur för delad åtkomst i `CREDENTIAL` är felaktig. Köra RESTORE HEADERONLY innan du återställer en databas för att kontrollera att SAS-nyckeln är korrekta.
Kontrollera att du tar bort inledande `?` från SAS-nyckeln genereras med hjälp av Azure-portalen.

### <a name="tooling"></a>Tooling

SQL Server Management Studio och SQL Server Data Tools har några problem vid åtkomst till hanterade instans. Alla verktygsuppsättning problem åtgärdas innan allmän tillgänglighet.

### <a name="incorrect-database-names"></a>Felaktig databasnamn

Hanterade instans kan visa guid-värde i stället för databasens namn under återställningen eller i vissa felmeddelanden. De här problemen åtgärdas innan allmän tillgänglighet.

### <a name="database-mail-profile"></a>Database mail-profilen
Det kan finnas en enda database mail-profilen och måste anropas `AzureManagedInstance_dbmail_profile`. Detta är en tillfällig begränsning som snart tas bort.

## <a name="next-steps"></a>Nästa steg

- Mer information om hanterade instansen finns [vad är en hanterad instans?](sql-database-managed-instance.md)
- För en funktioner och jämförelse lista, se [SQL vanliga funktioner](sql-database-features.md).
- En självstudiekurs finns [skapa en instans för hanterade](sql-database-managed-instance-tutorial-portal.md).
