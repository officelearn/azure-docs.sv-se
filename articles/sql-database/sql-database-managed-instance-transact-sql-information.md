---
title: Azure SQL Database Managed Instance T-SQL-skillnader | Microsoft Docs
description: Den här artikeln beskriver T-SQL-skillnader mellan Azure SQL Database Managed Instance och SQL Server.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: c23fbf0af7d1a15b0efee8af123150feb42c708e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966927"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database Managed Instance T-SQL skillnader från SQL Server 

Azure SQL Database Managed Instance tillhandahåller hög kompatibilitet med en lokal SQL Server Database Engine. De flesta SQL Server Database Engine-funktioner stöds i hanterade instanser. Eftersom det finns fortfarande några skillnader i syntaxen och beteende, den här artikeln sammanfattar och förklarar skillnaderna.
 - [T-SQL skillnader och funktioner som inte stöds](#Differences)
 - [Funktioner som har olika beteenden i Managed Instance](#Changes)
 - [Temporära begränsningar och kända problem](#Issues)

## <a name="Differences"></a> T-SQL-skillnader från SQL Server 

Det här avsnittet sammanfattas viktiga skillnader i T-SQL-syntaxen och beteende mellan hanterad instans och en lokal SQL Server Database Engine, samt funktioner som inte stöds.

### <a name="always-on-availability"></a>Ständig tillgänglighet

[Hög tillgänglighet](sql-database-high-availability.md) är inbyggd i Managed Instance och kan inte styras av användare. Följande uttryck stöds inte:
 - [SKAPA SLUTPUNKT... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
 - [SKAPA TILLGÄNGLIGHETSGRUPP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
 - [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
 - [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
 - [Ange HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) -satsen i den [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) instruktionen

### <a name="auditing"></a>Granskning 
 
De viktigaste skillnaderna mellan SQL-granskning på hanterad instans, Azure SQL Database och SQL Server lokalt är:
- I Managed Instance fungerar SQL-granskning på servernivå och lagrar `.xel` filer på Azure blob storage-konto.  
- I Azure SQL Database fungerar SQL-granskning på databasnivå.
- I SQL Server lokalt / virtuell dator, SQL-granskning fungerar på servernivå, men lagrar händelser på filer system/windows-händelseloggar.  
  
XEvent granskning i Managed Instance stöder prestandamål i Azure blob storage. Fil- och windows-loggar stöds inte.    
 
Nyckeln skillnader i den `CREATE AUDIT` syntaxen för granskning till Azure blob storage är:
- En ny syntax `TO URL` tillhandahålls och kan du ange URL: en för Azure blob Storage-behållare där `.xel` filer ska placeras 
- Syntaxen `TO FILE` stöds inte eftersom hanterad instans inte kan komma åt Windows-filresurser. 
 
Mer information finns i:  
- [SKAPA SERVERGRANSKNING](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Backup 

Hanterad instans har automatisk säkerhetskopiering och kan du skapa fullständiga databasen `COPY_ONLY` säkerhetskopior. Differentiell, logg och av filögonblicksbilder stöds inte.  
- Hanterad instans kan säkerhetskopiera en databas bara till ett Azure Blob Storage-konto: 
 - Endast `BACKUP TO URL` stöds 
 - `FILE`, `TAPE`, och enheter för säkerhetskopiering stöds inte  
- De flesta av allmänna `WITH` alternativ som stöds 
 - `COPY_ONLY` är obligatoriskt
 - `FILE_SNAPSHOT` Stöds inte  
 - Bandalternativ: `REWIND`, `NOREWIND`, `UNLOAD`, och `NOUNLOAD` stöds inte 
 - Log-specifika alternativ: `NORECOVERY`, `STANDBY`, och `NO_TRUNCATE` stöds inte 
 
Begränsningar:  
- Hanterad instans kan säkerhetskopiera en databas till en säkerhetskopia med upp till 32 stripe, vilket räcker för databaser upp till 4 TB om komprimering av säkerhetskopior används.
- Maxstorlek för säkerhetskopiering stripe är 195 GB (högsta blob storlek). Öka antalet stripe i backup kommandot för att minska Stripestorleken på enskilda och stannar inom den här gränsen. 

> [!TIP]
> Att kringgå den här begränsningen lokalt, säkerhetskopiering till `DISK` i stället för säkerhetskopiering till `URL`, ladda upp säkerhetskopian blob och sedan återställa. Återställa stöder större filer eftersom en annan blob-typ används.  

Information om säkerhetskopior med hjälp av T-SQL finns i [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

### <a name="buffer-pool-extension"></a>Buffertpooltillägget 
 
- [Buffra pool tillägget](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) stöds inte.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` stöds inte. Se [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql). 
 
### <a name="bulk-insert--openrowset"></a>Massinfogning / openrowset

Hanterad instans kan inte komma åt delade filer och mappar i Windows, så att filerna måste importeras från Azure blob storage.
- `DATASOURCE` Du måste ange i `BULK INSERT` kommandot när du importerar filer från Azure blob storage. Se [MASSINFOGNING](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` Du måste ange i `OPENROWSET` fungerar när du läser en innehållet i en fil från Azure blob storage. Se [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
 
### <a name="certificates"></a>Certifikat 

Managed Instance kan inte komma åt filresurser och Windows-mappar. Följande begränsningar gäller: 
- `CREATE FROM`/`BACKUP TO` filen stöds inte för certifikat
- `CREATE`/`BACKUP` certifikat från `FILE` / `ASSEMBLY` stöds inte. Privata nyckelfilerna kan inte användas.  
 
Se [skapa certifikat](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) och [säkerhetskopiera certifikat](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
> [!TIP]
> Lösning: skript certifikat och privata nycklar, lagra som SQL-filen och skapa från binär: 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

Managed Instance kan inte komma åt filresurser och Windows-mappar. Följande begränsningar gäller: 
- Endast `CREATE ASSEMBLY FROM BINARY` stöds. Se [skapa sammansättningen från binär](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` stöds inte. Se [skapa sammansättningen från filen](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` Det går inte att referera till filer. Se [ändring av sammansättningen](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).
 
### <a name="compatibility-levels"></a>Efterlevnadsnivåer 
 
- Kompatibilitetsnivå som stöds är: 100, 110, 120, 130, 140  
- Efterlevnadsnivåer under 100 stöds inte. 
- Standardkompatibilitetsnivån för nya databaser är 140. För återställda databaser påverkas kompatibilitetsnivå inte om den var 100 och högre.

Se [ALTER Databaskompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
 
### <a name="credential"></a>Autentiseringsuppgift 
 
Endast Azure Key Vault och `SHARED ACCESS SIGNATURE` identiteter stöds. Windows-användare stöds inte.
 
Se [skapa AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) och [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql). 
 
### <a name="cryptographic-providers"></a>Kryptografiproviders

Hanterad instans kan inte komma åt filer så det inte går att skapa kryptografiproviders:
- `CREATE CRYPTOGRAPHIC PROVIDER` stöds inte. Se [skapa KRYPTOGRAFIPROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` stöds inte. Se [ALTER KRYPTOGRAFIPROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql). 

### <a name="collation"></a>Sortering 
 
Server-sorteringen är `SQL_Latin1_General_CP1_CI_AS` och kan inte ändras. Se [sorteringar](https://docs.microsoft.com/sql/t-sql/statements/collations).
 
### <a name="database-options"></a>Databasalternativ 
 
- Flera loggfiler stöds inte. 
- InMemory-objekt stöds inte på tjänstnivån generell användning.  
- Det finns en gräns på 280 filer per instans innebär max 280 filer per databas. Både data och loggfiler räknas mot den här gränsen.  
- Databasen får inte innehålla filgrupper som innehåller filestream-data.  Återställningen misslyckas om .bak innehåller `FILESTREAM` data.  
- Varje fil placeras i Azure Premium storage. I/o och dataflöde per fil beror på storleken på varje enskild fil på samma sätt som de gör för Azure Premium Storage-diskar. Se [Azure Premium-diskprestanda](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)  
 
#### <a name="create-database-statement"></a>CREATE DATABASE-instruktionen

Följande är `CREATE DATABASE` begränsningar: 
- Filer och filgrupper kan inte definieras.  
- `CONTAINMENT` alternativet stöds inte.  
- `WITH`alternativ stöds inte.  
   > [!TIP]
   > Som lösning kan du använda `ALTER DATABASE` när `CREATE DATABASE` ange databasalternativ att lägga till filer eller ange inneslutning.  

- `FOR ATTACH` alternativet stöds inte 
- `AS SNAPSHOT OF` alternativet stöds inte 

Mer information finns i [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instruktionen ALTER DATABASE

Vissa egenskaper för filer kan inte ange eller ändra:
- Filsökvägen kan inte anges i `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-instruktionen. Ta bort `FILENAME` från skriptet eftersom den hanterade instansen automatiskt placerar filerna.  
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
 
Odokumenterade DBCC-uttryck som är aktiverade i SQL Server stöds inte i hanterade instanser.
- `Trace Flags` stöds inte. Se [Spårningsflaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` stöds inte. Se [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` stöds inte. Se [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Distribuerade transaktioner

Varken MSDTC eller [elastiska transaktioner](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-transactions-overview) stöds för närvarande i Managed Instance.

### <a name="extended-events"></a>Extended Events 

Vissa Windows-specifika mål för XEvents stöds inte:
- `etw_classic_sync target` stöds inte. Store `.xel` filer på Azure blob-lagring. Se [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target). 
- `event_file target`stöds inte. Store `.xel` filer på Azure blob-lagring. Se [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target).

### <a name="external-libraries"></a>Externa bibliotek

I databasen R och Python externa bibliotek inte stöds ännu. Se [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

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

### <a name="full-text-semantic-search"></a>Semantisk fulltextsökning

[Semantisk sökning](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) stöds inte.

### <a name="linked-servers"></a>Länkade servrar
 
Länkade servrar i Managed Instance stöder begränsat antal mål: 
- Mål som stöds: SQLServer och SQL-databas
- Stöds inte mål: filer, Analysis Services och andra RDBMS.

Åtgärder

- Cross-instans skrivtransaktioner stöds inte.
- `sp_dropserver` har stöd för att släppa en länkad server. Se [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` funktionen kan användas för att köra frågor endast på SQL Server-instanser (antingen hanteras lokalt eller i virtuella datorer). Se [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` funktionen kan användas för att köra frågor endast på SQL Server-instanser (antingen hanteras lokalt eller i virtuella datorer). Endast `SQLNCLI`, `SQLNCLI11`, och `SQLOLEDB` värden som stöds som provider. Till exempel: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Se [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
 
### <a name="logins--users"></a>Inloggningar / användare 

- SQL-inloggningar som skapats `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, och `FROM SID` stöds. Se [skapa inloggningen](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Windows-inloggningar som skapats med `CREATE LOGIN ... FROM WINDOWS` syntaxen stöds inte.
- Azure Active Directory (Azure AD)-användare som skapade instansen har [obegränsad administratörsprivilegier](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts).
- Icke-Azure Active Directory (Azure AD) på databasnivå administratörer kan skapas med `CREATE USER ... FROM EXTERNAL PROVIDER` syntax. Se [skapa användare... FRÅN EXTERN PROVIDER](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)
 
### <a name="polybase"></a>Polybase

Externa tabeller som refererar till filer i HDFS- eller Azure blob storage stöds inte. Läs om hur Polybase [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikering 
 
Replikering är tillgänglig i offentlig förhandsversion på hanterad instans. Information om replikering finns i [SQL Server-replikering](http://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).
 
### <a name="restore-statement"></a>ÅTERSTÄLLA instruktionen 
 
- Syntax som stöds  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- Syntax som inte stöds 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- Källa  
 - `FROM URL` (Azure blob storage) är endast alternativ som stöds.
 - `FROM DISK`/`TAPE`/ enhet för säkerhetskopiering stöds inte.
 - Säkerhetskopior stöds inte. 
- `WITH` alternativ stöds inte (Nej `DIFFERENTIAL`, `STATS`osv.)     
- `ASYNC RESTORE` -Återställning fortsätter även om klientanslutning bryts. Om anslutningen bryts, du kan kontrollera `sys.dm_operation_status` visa status för en restore-åtgärd (samt skapa och släpp databas). Se [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  
 
Följande databasalternativ är set/åsidosätts och kan inte ändras senare:  
- `NEW_BROKER` (om broker inte är aktiverat i bak-filen)  
- `ENABLE_BROKER` (om broker inte är aktiverat i bak-filen)  
- `AUTO_CLOSE=OFF` (om en databas i bak-filen har `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (om en databas i bak-filen har `SIMPLE` eller `BULK_LOGGED` återställningsläge)
- Minnesoptimerade filgrupp läggs och anropas XTP om det inte var .bak källfilen  
- Alla befintliga minnesoptimerade filgrupp har bytt namn till XTP  
- `SINGLE_USER` och `RESTRICTED_USER` alternativ konverteras till `MULTI_USER`   
Begränsningar:  
- `.BAK` filer som innehåller flera säkerhetskopior kan inte återställas. 
- `.BAK` filer som innehåller flera loggfiler kan inte återställas. 
- Återställningen misslyckas om .bak innehåller `FILESTREAM` data.
- Säkerhetskopieringar som innehåller databaser som har aktiva InMemory-objekt kan inte återställas för tillfället.  
- Säkerhetskopieringar som innehåller databaser där någon gång i minnesobjekt fanns kan inte återställas för tillfället.   
- För närvarande det går inte att att återställa säkerhetskopior som innehåller databaser i skrivskyddat läge. Den här begränsningen tas bort snart.   
 
Information om återställning instruktioner finns i [ÅTERSTÄLLA instruktioner](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker 
 
- Cross-instans service broker stöds inte 
 - `sys.routes` – Förutsättning: Välj sys.routes-adress. Adressen måste vara lokal på varje väg. Se [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
 - `CREATE ROUTE` -Det går inte att `CREATE ROUTE` med `ADDRESS` än `LOCAL`. Se [skapa väg](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
 - `ALTER ROUTE` Det går inte att `ALTER ROUTE` med `ADDRESS` än `LOCAL`. Se [ALTER väg](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  
 
### <a name="service-key-and-service-master-key"></a>Tjänsten nycklar och tjänstens huvudnyckel 
 
- [Huvudnyckeln för säkerhetskopiering](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten) 
- [Återställ huvudnyckeln](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten) 
- [Tjänstens huvudnyckel backup](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten) 
- [Tjänstens huvudnyckel återställning](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten) 
 
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

- Inställningar för SQL Agent är skrivskyddade. Proceduren `sp_set_agent_properties` stöds inte i hanterade instanser.  
- Jobb – T-SQL-jobb steg stöds för närvarande
- Andra typer av jobb som steg för inte närvarande stöds (flera steg typer kommer att läggas till under den offentliga förhandsversionen).
  - Replikeringsjobb stöds inte, inklusive:
    - Transaktionsloggen läsare.  
    - Ögonblicksbild.
    - Distributören.  
    - Slå ihop.  
  - SSIS stöds inte ännu. 
  - Kön läsare stöds inte.  
  - Kommandogränssnitt (shell) stöds inte ännu. 
  - Hanterad instans kan inte komma åt externa resurser (till exempel nätverksresurser via robocopy).  
  - PowerShell stöds inte ännu.
  - Analysis Services stöds inte.  
- Meddelanden stöds delvis.
- E-postmeddelande stöds, kräver att du konfigurerar en Database Mail-profil. Det kan vara endast en database mail-profilen och den måste anropas `AzureManagedInstance_dbmail_profile` i offentlig förhandsversion (tillfällig begränsning).  
 - Personsökare stöds inte.  
 - NetSend stöds inte. 
 - Aviseringar stöds inte ännu inte.
 - Proxyservrar stöds inte.  
- Eventlog stöds inte. 
 
Följande funktioner stöds inte för närvarande, men kommer att aktiveras i framtiden:  
- Proxyservrar
- Schemalägga jobb på inaktiv CPU 
- Aktivering/inaktivering Agent
- Aviseringar

Information om SQL Server Agent finns i [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).
 
### <a name="tables"></a>Tabeller 

Följande stöds inte: 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

Information om att skapa och ändra tabeller finns i [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) och [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="Changes"></a> Funktionalitetsförändringar 
 
Följande variabler, uppgifter och vyer returnerar olika resultat:  
- `SERVERPROPERTY('EngineEdition')` Returnerar värdet 8. Den här egenskapen identifierar Managed Instance. Se [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Returnerar NULL, eftersom begreppet instans som det finns för SQL Server inte gäller för hanterad instans. Se [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Returnerar fullständigt DNS-läge för anslutningsbart'-namn till exempel Mina-managed-instance.wcus17662feb9ce98.database.windows.net. Se [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -Returnerar fullständig ”läge för anslutningsbart” DNS-namn, till exempel `myinstance.domain.database.windows.net` för egenskaperna ”name” och ”data_source”. Se [SYS. SERVRAR](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVICENAME` Returnerar NULL, eftersom begreppet tjänsten eftersom det finns för SQL Server inte gäller för hanterad instans. Se [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).   
- `SUSER_ID` stöds. Returnerar NULL om AAD-inloggning inte är i sys.syslogins. Se [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` stöds inte. Returnerar fel data (tillfälligt kända problem). Se [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 
- `GETDATE()` och andra inbyggda datum/tid-funktioner returnerar alltid tid i UTC-tidszonen. Se [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Kända problem och begränsningar

### <a name="tempdb-size"></a>TEMPDB-storlek

`tempdb` delas upp i 12 filer med maximal storlek på 14 GB per fil. Den här största storleken per fil inte kan ändras och nya filer kan inte läggas till `tempdb`. Den här begränsningen tas bort snart. Några frågor kan returnera ett fel om de behöver mer än 168 GB i `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Överstiger lagringsutrymme med små databasfiler

Varje hanterad instans har till 35 TB lagring som är reserverade för diskutrymme för Azure Premium och varje databasfil placeras på en separat fysisk disk. Diskstorlekar kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Outnyttjat utrymme på disken debiteras inte, men den totala summan av Azure Premium-diskstorlekar får inte överskrida 35 TB. I vissa fall kanske en hanterad instans som inte kräver 8 TB totalt överskrider 35 TB Azure begränsa lagringsstorleken, på grund av interna fragmentering. 

En hanterad instans kan till exempel ha en fil 1,2 TB i storlek som placeras på en disk med 4 TB och 248 filer 1 GB moduler storlek som är placerade på separata 128 GB-diskar. I det här exemplet 
* den totala diskstorleken för lagring är 1 x 4 TB + 248 x 128 GB = 35 TB. 
* Det totala reserverade utrymmet för databaser på instansen är 1 x 1.2 TB + 248 x 1 GB = 1,4 TB.
Detta visar att under vissa omständigheter på grund av en särskild distribution av filer, en hanterad instans kan nå 35 TB reserverade för ansluten Azure Premium Disk när du kanske inte den ska. 

I det här exemplet befintliga databaser fortsätter att fungera och kan växa utan problem, förutsatt att nya filer inte har lagts till. Men nya databaser kunde inte skapas eller återställas eftersom det inte finns tillräckligt med utrymme för nya diskenheter, även om den totala storleken på alla databaser inte når storleksgränsen för instansen. Felet som returneras är i så fall oklart.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Felaktig konfiguration av SAS-nyckel under databasen återställa

`RESTORE DATABASE` som läser bak-filen kan ständigt nya försök för att läsa bak-filen och returnerar fel efter lång tidsperiod om signatur för delad åtkomst i `CREDENTIAL` är felaktig. Köra RESTORE HEADERONLY innan du återställer en databas för att se till att SAS-nyckel är korrekt.
Kontrollera att du tar bort ledande `?` från SAS-nyckeln som genererades med hjälp av Azure portal.

### <a name="tooling"></a>Verktyg

SQL Server Management Studio och SQL Server Data Tools kan ha några problem vid anslutning till hanterad instans. Alla verktyg problem åtgärdas före den allmänt tillgängliga.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Felaktig databasnamn i vissa vyer, loggar och meddelanden

Visa GUID databasidentifierare i stället för faktiska databasnamn flera systemvyer, prestandaräknare, felmeddelanden, XEvents och poster i felloggen. Förlita dig inte på dessa GUID-identifierare eftersom de skulle ersättas med faktiska databasnamn i framtiden.

### <a name="database-mail-profile"></a>Database mail-profilen
Det kan vara endast en database mail-profilen och den måste anropas `AzureManagedInstance_dbmail_profile`. Det här är en tillfällig begränsning som kommer att tas bort snart.

### <a name="error-logs-are-not-persisted"></a>Felloggar är inte beständiga
Felloggarna som är tillgängliga i hanterade instansen har inte sparats och deras storlek ingår inte i den maximala lagringsgränsen. Felloggarna kan raderas automatiskt vid redundans.

### <a name="error-logs-are-verbose"></a>Felloggar finns utförlig
Hanterad instans placerar utförlig information i felloggarna och många av dem är inte relevanta. Att kommer minska mängden information i felloggarna i framtiden.

**Lösning**: använda en anpassad procedur för att läsa felloggar som filter ut vissa icke-relevanta poster. Mer information finns i [Azure SQL DB Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-is-not-supported"></a>Transaktions-Scope på två databaser inom samma instans stöds inte
`TransactionScope` klassen i .net fungerar inte om två frågor skickas till de två databaserna inom samma instans under samma transaktionsomfattning:

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Även om den här koden fungerar med data i samma instans krävs MSDTC.

**Lösning**: använda [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) använda andra database i kontext och anslutning istället för att använda två anslutningar.

### <a name="clr-modules-and-linked-servers-sometime-cannot-reference-local-ip-address"></a>CLR-moduler och en stund länkade servrar kan inte referera till lokal IP-adress
CLR-moduler som placerats i Managed Instance och länkade servrar/distribuerade frågor som refererar till aktuell instans någon gång det går inte att matcha IP-Adressen för den lokala instansen. Det här är ett tillfälligt fel.

**Lösning**: Använd kontext anslutningar i CLR-modulen om möjligt.

## <a name="next-steps"></a>Nästa steg

- Mer information om Managed Instance finns [vad är en hanterad instans?](sql-database-managed-instance.md)
- För en funktioner och jämförelse lista, se [SQL vanliga funktioner](sql-database-features.md).
- En Snabbstart som visar hur du skapar en ny hanterad instans, se [skapar en hanterad instans](sql-database-managed-instance-get-started.md).
