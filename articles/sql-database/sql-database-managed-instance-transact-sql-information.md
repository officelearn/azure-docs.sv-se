---
title: Azure SQL Database Managed Instance T-SQL-skillnader | Microsoft Docs
description: Den här artikeln beskriver T-SQL-skillnader mellan en hanterad instans i Azure SQL Database och SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5f476aa571ba2827cbe6f4e4f258545b5e9d3ba1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106373"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database Managed Instance T-SQL skillnader från SQL Server

Den här artikeln sammanfattar och förklarar skillnader i syntaxen och beteende mellan Azure SQL Database Managed Instance och en lokal SQL Server Database Engine. <a name="Differences"></a>

- [Tillgänglighet](#availability) inklusive skillnaderna i [alltid på](#always-on-availability) och [säkerhetskopior](#backup),
- [Security](#security) inklusive skillnaderna i [granskning](#auditing), [certifikat](#certificates), [autentiseringsuppgifter](#credential), [kryptografiproviders](#cryptographic-providers), [Inloggningar / användare](#logins--users), [nyckel och huvudnyckeln för tjänsten](#service-key-and-service-master-key),
- [Konfigurationen](#configuration) inklusive skillnaderna i [buffra pool tillägget](#buffer-pool-extension), [sortering](#collation), [kompatibilitetsnivå](#compatibility-levels),[databas spegling](#database-mirroring), [databasalternativ](#database-options), [SQL Server Agent](#sql-server-agent), [Tabellalternativ](#tables),
- [Lär dig om funktionerna](#functionalities) inklusive [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuerade transaktioner](#distributed-transactions), [ Utökade händelser](#extended-events), [externa bibliotek](#external-libraries), [Filestream- och Filetable](#filestream-and-filetable), [semantiska fulltextsökning](#full-text-semantic-search), [länkade servrar](#linked-servers), [Polybase](#polybase), [replikering](#replication), [ÅTERSTÄLLA](#restore-statement), [Service Broker](#service-broker), [ Lagrade procedurer, funktioner och utlösare](#stored-procedures-functions-triggers),
- [Funktioner som har olika beteenden i hanterade instanser](#Changes)
- [Temporära begränsningar och kända problem](#Issues)

Alternativ för distribution av Managed Instance tillhandahåller hög kompatibilitet med en lokal SQL Server Database Engine. De flesta av SQL Server database engine-funktioner stöds i en hanterad instans.

![Migrering](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Tillgänglighet

### <a name="always-on-availability"></a>Alltid på

[Hög tillgänglighet](sql-database-high-availability.md) är inbyggd i Managed Instance och kan inte styras av användare. Följande uttryck stöds inte:

- [SKAPA SLUTPUNKT... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [SKAPA TILLGÄNGLIGHETSGRUPP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [Ange HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) -satsen i den [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) instruktionen

### <a name="backup"></a>Backup

Hanterade instanser har automatisk säkerhetskopiering och låta användare skapa fullständiga databasen `COPY_ONLY` säkerhetskopior. Differentiell, logg och av filögonblicksbilder stöds inte.

- Med en hanterad instans kan du säkerhetskopiera en databasinstans endast till ett Azure Blob Storage-konto:
  - Endast `BACKUP TO URL` stöds
  - `FILE`, `TAPE`, och enheter för säkerhetskopiering stöds inte  
- De flesta av allmänna `WITH` alternativ som stöds
  - `COPY_ONLY` är obligatoriskt
  - `FILE_SNAPSHOT` Stöds inte
  - Bandalternativ: `REWIND`, `NOREWIND`, `UNLOAD`, och `NOUNLOAD` stöds inte
  - Log-specifika alternativ: `NORECOVERY`, `STANDBY`, och `NO_TRUNCATE` stöds inte

Begränsningar:  

- Med en hanterad instans, du kan säkerhetskopiera en databasinstans till en säkerhetskopia med upp till 32 stripe, vilket räcker för databaser upp till 4 TB om komprimering av säkerhetskopior används.
- Max säkerhetskopiering stripe storlek med den `BACKUP` kommandot i en hanterad instans är 195 GB (högsta blob storlek). Öka antalet stripe i backup kommandot för att minska Stripestorleken på enskilda och stannar inom den här gränsen.

    > [!TIP]
    > För att undvika denna begränsning när du säkerhetskopierar en databas från antingen SQL Server i en lokal miljö eller i en virtuell dator, kan du göra följande:
    >
    > - Säkerhetskopiering till `DISK` i stället för att säkerhetskopiera till `URL`
    > - Överför säkerhetskopieringsfilerna till Blob storage
    > - Återställa till den hanterade instansen
    >
    > Den `Restore` kommandot i en hanterade instanser stöder större storlekar för blob i de säkerhetskopiera filerna eftersom en annan blob-typ används för lagring av överförda säkerhetskopian.

Information om säkerhetskopior med hjälp av T-SQL finns i [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Säkerhet

### <a name="auditing"></a>Granskning

De viktigaste skillnaderna mellan granskning i databaser i Azure SQL Database och databaser i SQL Server är:

- Med alternativet för hanterad instans-distribution i Azure SQL Database-granskning fungerar på servernivå och lagrar `.xel` loggfiler i Azure Blob storage.
- Med enkel databas och elastisk pool distributionsalternativ i Azure SQL Database-granskning fungerar på databasnivå.
- I SQL Server lokalt per virtuell datorer, granska fungerar på servern nivå, men lagrar händelser på filer system/windows-händelseloggar.
  
XEvent granskning i Managed Instance stöder prestandamål i Azure Blob storage. Fil- och windows-loggar stöds inte.

Nyckeln skillnader i den `CREATE AUDIT` syntaxen för granskning till Azure Blob storage är:

- En ny syntax `TO URL` tillhandahålls och kan du ange URL: en för Azure blob Storage-behållare där `.xel` filer ska placeras
- Syntaxen `TO FILE` stöds inte eftersom en hanterad instans inte kan komma åt Windows-filresurser.

Mer information finns i:  

- [SKAPA SERVERGRANSKNING](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikat

En hanterad instans kan inte komma åt delade filer och mappar i Windows, så gäller följande begränsningar:

- `CREATE FROM`/`BACKUP TO` filen stöds inte för certifikat
- `CREATE`/`BACKUP` certifikat från `FILE` / `ASSEMBLY` stöds inte. Privata nyckelfilerna kan inte användas.  

Se [skapa certifikat](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) och [säkerhetskopiera certifikat](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Lösning**: skript certifikat och privata nycklar, lagra som SQL-filen och skapa från binär:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Autentiseringsuppgift

Endast Azure Key Vault och `SHARED ACCESS SIGNATURE` identiteter stöds. Windows-användare stöds inte.

Se [skapa AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) och [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kryptografiproviders

En hanterad instans kan inte komma åt filer så det inte går att skapa kryptografiproviders:

- `CREATE CRYPTOGRAPHIC PROVIDER` stöds inte. Se [skapa KRYPTOGRAFIPROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` stöds inte. Se [ALTER KRYPTOGRAFIPROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins--users"></a>Inloggningar / användare

- SQL-inloggningar som skapats `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, och `FROM SID` stöds. Se [skapa inloggningen](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD)-server-huvudkonton (inloggningar) skapas med [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) syntax eller [skapa från ANVÄNDARINLOGGNING [Azure AD-kontoinloggning]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) syntax som stöds (**offentlig förhandsversion** ). Det här är inloggningar som skapats på servernivå.

    Hanterad instans har stöd för Azure AD-huvudkonton med syntaxen `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Detta kallas även för Azure AD finns databasanvändare.

- Windows-inloggningar som skapats med `CREATE LOGIN ... FROM WINDOWS` syntaxen stöds inte. Använd Azure Active Directory-inloggningar och användare.
- Azure AD-användare som skapade instansen har [obegränsad administratörsprivilegier](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Icke-Azure Active Directory (Azure AD) på databasnivå administratörer kan skapas med `CREATE USER ... FROM EXTERNAL PROVIDER` syntax. Se [skapa användare... FRÅN EXTERN PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Azure AD-server-huvudkonton (inloggningar) stöder SQL-funktioner i en MI-instans. Funktioner som kräver interaktion mellan-instans, oavsett om inom samma Azure AD-klient eller annan klient inte stöds för Azure AD-användare. Exempel på sådana funktioner är:

  - Transaktionsreplikering i SQL och
  - Länka Server

- Ange en Azure AD-inloggningen som mappades till en Azure AD-grupp som databasens ägare inte stöds.
- Personifiering av Azure AD-servernivå säkerhetsobjekt som med andra Azure AD-huvudkonton stöds, till exempel den [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) satsen. Kör som begränsning:

  - EXECUTE AS USER stöds inte för Azure AD-användare när namnet skiljer sig från inloggningsnamn. Till exempel när användaren har skapats via syntax skapa [myAadUser] från ANVÄNDARINLOGGNING [john@contoso.com], och personifiering görs via EXEC AS USER = _myAadUser_. När du skapar en **USER** (Användare) från ett Azure AD-serverhuvudkonto (inloggning) anger du samma user_name som login_name från **LOGIN** (Inloggning).
  - Endast SQL-huvudkonton på servernivå (inloggningar) som ingår i rollen `sysadmin` kan utföra följande åtgärder som riktar in sig på Azure AD-huvudkonton:

    - KÖRA SOM ANVÄNDARE
    - KÖRA SOM INLOGGNING

- **Offentlig förhandsversion** begränsningar för Azure AD-server-huvudkonton (inloggningar):

  - Active Directory-administratör begränsningar för hanterad instans:

    - Azure AD-administratören som används för att ställa in den hanterade instansen kan inte användas för att skapa en Azure AD-server principal (inloggning) i den hanterade instansen. Du måste skapa det första Azure AD-serverhuvudkontot (inloggning) med hjälp av ett SQL Server-konto som är `sysadmin`. Det här är en tillfällig begränsning som kommer att tas bort när Azure AD-serverhuvudkonton (inloggningar) blir allmänt tillgängliga. Följande felmeddelande visas om du försöker använda en Azure AD-administratörskonto för att skapa inloggningen: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - För närvarande är den första Azure AD-kontoinloggning som skapats i master DB måste ha skapats av den SQL Server-konto av standardtyp (icke-Azure AD) som är en `sysadmin` med hjälp av den [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) från extern PROVIDER. Efter allmän tillgänglighet, den här begränsningen kommer att borttagna och har en första Azure AD-inloggning kan skapas med Active Directory-administratör för hanterad instans.
    - DacFx (export/Import) användas med SQL Server Management Studio (SSMS) eller SqlPackage stöds inte för Azure AD-inloggningar. Den här begränsningen kommer att tas bort när Azure AD-server-huvudkonton (inloggningar) fungerar GA.
    - Med hjälp av Azure AD-server-huvudkonton (inloggningar) med SSMS

      - Skript Azure AD-inloggningar (med valfri autentiserad inloggning) stöds inte.
      - IntelliSense inte kan identifiera den **skapa inloggning från en extern PROVIDER** instruktionen och visar en röd understrykning.

- Endast huvudsaklig inloggning på servernivå (som skapats av den hanterade instansen etableringsprocessen), medlemmar i serverrollerna (`securityadmin` eller `sysadmin`), eller andra inloggningar med ALTER ANY LOGIN-behörighet på servernivå kan skapa Azure AD-server säkerhetsobjekt (inloggningar) i master-databasen för hanterad instans.
- Om inloggningen är ett SQL-huvudkonto kan inloggningar som ingår i rollen `sysadmin` använda kommandot create för att skapa inloggningar för en Azure AD-konto.
- Azure AD-inloggning måste vara medlem i en Azure AD i samma katalog används för Azure SQL Managed Instance.
- Azure AD-server-huvudkonton (inloggningar) är synliga i object explorer som börjar med SSMS 18.0 preview 5.
- Överlappande Azure AD-huvudkonton server (inloggningar) med ett administratörskonto för Azure AD är tillåtet. Azure AD-server-huvudkonton (inloggningar) högre prioritet än Azure AD-administratör när matchning av de primära och tillämpa behörigheterna för den hanterade instansen.
- Under autentiseringen används följande sekvens för att lösa det autentiserande huvudnamnet:

    1. Om Azure AD-kontot finns som direkt mappade bevilja åtkomst till Azure AD tjänstens huvudnamn (inloggning) (finns i sys.server_principals som typen ”E”), och tillämpa behörigheter för Azure AD tjänstens huvudnamn (inloggning).
    2. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till Azure AD tjänstens huvudnamn (inloggning) (finns i sys.server_principals som skriver ”X”), bevilja åtkomst och tillämpa behörigheter för inloggningen för Azure AD-grupp.
    3. Om Azure AD-kontot är en särskild portal konfigurerade gäller Azure AD-administratör för hanterad instans (inte finns i systemvyer för hanterad instans), särskilt fast behörigheterna för Azure AD-administratör för hanterad instans (bakåtkompatibelt läge).
    4. Om Azure AD-kontot finns som direkt mappade till Azure AD-användare i en databas (i sys.database_principals som typen ”E”), kan du bevilja åtkomst och tillämpa behörigheter för Azure AD-databasanvändare.
    5. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till en Azure AD-användare i en databas (i sys.database_principals som typen ”X”) kan bevilja åtkomst och tillämpa behörigheter för inloggningen för Azure AD-grupp.
    6. Om det finns en Azure AD-inloggningen som mappades till en Azure AD-användarkonto eller ett konto för Azure AD-grupp, tillämpas matchning för den autentiserande användaren, alla behörigheter från den här Azure AD-kontoinloggning.

### <a name="service-key-and-service-master-key"></a>Tjänsten nycklar och tjänstens huvudnyckel

- [Huvudnyckeln för säkerhetskopiering](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten)
- [Återställ huvudnyckeln](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten)
- [Tjänstens huvudnyckel backup](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten)
- [Tjänstens huvudnyckel återställning](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten)

## <a name="configuration"></a>Konfiguration

### <a name="buffer-pool-extension"></a>Buffertpooltillägget

- [Buffra pool tillägget](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) stöds inte.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` stöds inte. Se [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortering

Standardsortering för instansen är `SQL_Latin1_General_CP1_CI_AS` och kan anges som en parameter för skapandet. Se [sorteringar](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Efterlevnadsnivåer

- Kompatibilitetsnivå som stöds är: 100, 110, 120, 130, 140  
- Efterlevnadsnivåer under 100 stöds inte.
- Standardkompatibilitetsnivån för nya databaser är 140. För återställda databaser påverkas kompatibilitetsnivå inte om den var 100 och högre.

Se [ALTER Databaskompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Databasspegling

Databasspegling stöds inte.

- `ALTER DATABASE SET PARTNER` och `SET WITNESS` alternativ stöds inte.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` stöds inte.

Mer information finns i [ALTER DATABASE SET PARTNER och SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) och [CREATE ENDPOINT... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Databasalternativ

- Flera loggfiler stöds inte.
- InMemory-objekt stöds inte på tjänstnivån generell användning.  
- Det finns en gräns på 280 filer per generella instans innebär max 280 filer per databas. Både data och loggfiler filer generellt syfte nivå räknas mot den här gränsen. [Kritiska affärsnivå stöder 32 767 filer per databas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Databasen får inte innehålla filgrupper som innehåller filestream-data.  Återställningen misslyckas om .bak innehåller `FILESTREAM` data.  
- Varje fil placeras i Azure Blob storage. I/o och dataflöde per fil beror på storleken på varje enskild fil.  

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

- Filsökvägen kan inte anges i `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-instruktionen. Ta bort `FILENAME` från skriptet eftersom en hanterad instans automatiskt placerar filerna.  
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

Mer information finns i [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Inställningar för SQL Agent är skrivskyddade. Proceduren `sp_set_agent_properties` stöds inte i hanterade instanser.  
- Jobb
  - Steg för T-SQL-jobb stöds.
  - Följande replikeringsjobb stöds:
    - Transaktionsloggen läsare
    - Ögonblicksbild
    - Distributören
  - Steg för SSIS-jobb som stöds
  - Andra typer av jobb steg stöds inte för närvarande, inklusive:
    - Jobbsteg för sammanslagen replikering stöds inte.  
    - Kön läsare stöds inte.  
    - Kommandogränssnitt (shell) stöds inte ännu.
  - Hanterade instanser kan inte komma åt externa resurser (till exempel nätverksresurser via robocopy).  
  - Analysis Services stöds inte.
- Meddelanden stöds delvis.
- E-postmeddelande stöds, kräver att du konfigurerar en Database Mail-profil. SQL Agent kan använda endast en database mail-profilen och den måste anropas `AzureManagedInstance_dbmail_profile`.  
  - Personsökare stöds inte.  
  - NetSend stöds inte.
  - Aviseringar stöds inte ännu.
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

## <a name="functionalities"></a>Funktioner

### <a name="bulk-insert--openrowset"></a>Massinfogning / openrowset

En hanterad instans kan inte komma åt delade filer och mappar i Windows, så att filerna måste importeras från Azure Blob storage:

- `DATASOURCE` Du måste ange i `BULK INSERT` kommandot när du importerar filer från Azure Blob storage. Se [MASSINFOGNING](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` Du måste ange i `OPENROWSET` fungerar när du läser en innehållet i en fil från Azure Blob storage. Se [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

En hanterad instans kan inte komma åt delade filer och mappar i Windows, så gäller följande begränsningar:

- Endast `CREATE ASSEMBLY FROM BINARY` stöds. Se [skapa sammansättningen från binär](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` stöds inte. Se [skapa sammansättningen från filen](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` Det går inte att referera till filer. Se [ändring av sammansättningen](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Odokumenterade DBCC-uttryck som är aktiverade i SQL Server stöds inte i hanterade instanser.

- `Trace Flags` stöds inte. Se [Spårningsflaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` stöds inte. Se [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` stöds inte. Se [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Distribuerade transaktioner

Varken MSDTC eller [elastiska transaktioner](sql-database-elastic-transactions-overview.md) stöds för närvarande i hanterade instanser.

### <a name="extended-events"></a>Extended Events

Vissa Windows-specifika mål för XEvents stöds inte:

- `etw_classic_sync target` stöds inte. Store `.xel` filer på Azure blob-lagring. Se [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file target` stöds inte. Store `.xel` filer på Azure blob-lagring. Se [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externa bibliotek

I databasen R och Python externa bibliotek inte stöds ännu. Se [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream och Filetable

- FileStream-data stöds inte.
- Databasen får inte innehålla filgrupper med `FILESTREAM` data.
- `FILETABLE` stöds inte.
- Tabeller kan inte ha `FILESTREAM` typer.
- Följande funktioner stöds inte:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Mer information finns i [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) och [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Semantisk fulltextsökning

[Semantisk sökning](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) stöds inte.

### <a name="linked-servers"></a>Länkade servrar

Länkade servrar i hanterade instanser stöder ett begränsat antal mål:

- Stöds mål: SQLServer och SQL-databas
- Stöds inte mål: filer, Analysis Services och andra RDBMS.

Åtgärder

- Cross-instans skrivtransaktioner stöds inte.
- `sp_dropserver` har stöd för att släppa en länkad server. See [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET` funktionen kan användas för att köra frågor endast på SQL Server-instanser (antingen hanteras lokalt eller i virtuella datorer). Se [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE` funktionen kan användas för att köra frågor endast på SQL Server-instanser (antingen hanteras lokalt eller i virtuella datorer). Endast `SQLNCLI`, `SQLNCLI11`, och `SQLOLEDB` värden som stöds som provider. Till exempel: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Se [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>Polybase

Externa tabeller som refererar till filer i HDFS- eller Azure Blob-lagring stöds inte. Läs om hur Polybase [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikering

Replikering är tillgänglig för en förhandsversion för hanterad instans. Information om replikering finns i [SQL Server-replikering](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

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
  - `FROM URL` (Azure Blob storage) är bara stöds.
  - `FROM DISK`/`TAPE`/ enhet för säkerhetskopiering stöds inte.
  - Säkerhetskopior stöds inte.
- `WITH` alternativ stöds inte (Nej `DIFFERENTIAL`, `STATS`osv.)
- `ASYNC RESTORE` -Återställning fortsätter även om klientanslutning bryts. Om anslutningen bryts, du kan kontrollera `sys.dm_operation_status` visa status för en restore-åtgärd (samt skapa och släpp databas). Se [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

Följande databasalternativ är set/åsidosätts och kan inte ändras senare:  

- `NEW_BROKER` (om broker inte är aktiverad i bak-filen)  
- `ENABLE_BROKER` (om broker inte är aktiverad i bak-filen)  
- `AUTO_CLOSE=OFF` (om en databas i bak-filen har `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (om en databas i bak-filen har `SIMPLE` eller `BULK_LOGGED` återställningsläge)
- Minnesoptimerade filgrupp läggs och kallas XTP om den inte i .bak källfilen  
- Alla befintliga minnesoptimerade filgrupp har bytt namn till XTP  
- `SINGLE_USER` och `RESTRICTED_USER` alternativ konverteras till `MULTI_USER`

Begränsningar:  

- `.BAK` filer som innehåller flera säkerhetskopior kan inte återställas.
- `.BAK` filer som innehåller flera loggfiler kan inte återställas.
- Återställningen misslyckas om .bak innehåller `FILESTREAM` data.
- Säkerhetskopieringar som innehåller databaser som har aktiva InMemory-objekt kan inte återställas på allmän-instansen.  
Information om återställning instruktioner finns i [ÅTERSTÄLLA instruktioner](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Cross-instans service broker stöds inte:

- `sys.routes` – Förutsättning: Välj sys.routes-adress. Adressen måste vara lokal på varje väg. Se [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE` -Du kan inte använda `CREATE ROUTE` med `ADDRESS` än `LOCAL`. Se [skapa väg](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE` Det går inte att `ALTER ROUTE` med `ADDRESS` än `LOCAL`. Se [ALTER väg](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="stored-procedures-functions-triggers"></a>Lagrade procedurer, funktioner, utlösare

- `NATIVE_COMPILATION` stöds inte i nivån generell användning.
- Följande [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) alternativ stöds inte:
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` stöds inte. Se [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` stöds inte. Se [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` stöds inte, inklusive `sp_addextendedproc`  och `sp_dropextendedproc`. Se [utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, och `sp_detach_db` stöds inte. Se [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), och [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Funktionalitetsförändringar

Följande variabler, uppgifter och vyer returnerar olika resultat:

- `SERVERPROPERTY('EngineEdition')` Returnerar värdet 8. Den här egenskapen identifierar en hanterad instans. Se [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Returnerar NULL, eftersom begreppet instans som det finns för SQL Server inte gäller för en hanterad instans. Se [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Returnerar fullständigt DNS-läge för anslutningsbart'-namn till exempel Mina-managed-instance.wcus17662feb9ce98.database.windows.net. Se [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -Returnerar fullständig ”läge för anslutningsbart” DNS-namn, till exempel `myinstance.domain.database.windows.net` för egenskaperna ”name” och ”data_source”. Se [SYS. SERVRAR](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Returnerar NULL, eftersom begreppet tjänsten eftersom det finns för SQL Server inte gäller för en hanterad instans. Se [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` stöds. Returnerar NULL om Azure AD-inloggningen inte är i sys.syslogins. Se [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` stöds inte. Returnerar fel data (tillfälligt kända problem). Se [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).

## <a name="Issues"></a> Kända problem och begränsningar

### <a name="tempdb-size"></a>TEMPDB-storlek

Största filstorlek för `tempdb` får inte överskrida 24 GB/core på nivån generell användning. Max `tempdb` storleken på nivån affärskritisk är begränsad med lagringsstorlek instans. `tempdb` är alltid att dela upp till 12 datafiler. Den här största storleken per fil inte kan ändras och nya filer som kan läggas till `tempdb`. Några frågor kan returnera ett fel om de behöver mer än 24GB / kärna i `tempdb`.

### <a name="cannot-restore-contained-database"></a>Det går inte att återställa innesluten databas

Hanterad instans kan inte återställa [inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Point-in-time-återställning av befintliga inneslutna databaser fungerar inte på hanterad instans. Det här problemet tas snart och fram till dess rekommenderar vi att ta bort inneslutningsalternativet från databaser som är placerade på hanterad instans och Använd inte inneslutningsalternativet för produktionsdatabaserna.

### <a name="exceeding-storage-space-with-small-database-files"></a>Överstiger lagringsutrymme med små databasfiler

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, och `RESTORE DATABASE` instruktioner misslyckas, eftersom instansen kan uppnår sin gräns för Azure Storage.

Varje allmänt syfte hanterad instans har till 35 TB lagring som är reserverade för diskutrymme för Azure Premium och varje databasfil placeras på en separat fysisk disk. Diskstorlekar kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Outnyttjat utrymme på disken debiteras inte, men den totala summan av Azure Premium-diskstorlekar får inte överskrida 35 TB. I vissa fall kanske en hanterad instans som inte kräver 8 TB totalt överskrider 35 TB Azure begränsa lagringsstorleken, på grund av interna fragmentering.

En allmän syfte hanterad instans kan till exempel ha en fil 1,2 TB i storlek som placeras på en disk med 4 TB och 248 filer (varje 1 GB i storlek) som är placerade på separata 128 GB-diskar. I det här exemplet:

- Den totala allokerade disk lagringsstorleken är 1 x 4 TB + 248 x 128 GB = 35 TB.
- Det totala reserverade utrymmet för databaser på instansen är 1 x 1.2 TB + 248 x 1 GB = 1,4 TB.

Detta visar som under vissa omständigheter på grund av en specifik distribution av filer, en hanterad instans kan nå 35 TB reserverade för ansluten Azure Premium Disk när du kanske inte den ska.

I det här exemplet befintliga databaser fortsätter att fungera och kan växa utan problem, förutsatt att nya filer inte har lagts till. Men nya databaser kunde inte skapas eller återställas eftersom det inte finns tillräckligt med utrymme för nya diskenheter, även om den totala storleken på alla databaser inte når storleksgränsen för instansen. Felet som returneras är i så fall oklart.

Du kan [identifiera antalet återstående filer](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) med systemvyer. Om du ansluter till den här gränsen försöker [tom och ta bort några av de mindre filer med hjälp av DBCC SHRINKFILE instruktionen](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) eller växla till [affärskritisk nivå som inte har den här gränsen](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Felaktig konfiguration av SAS-nyckel under databasen återställa

`RESTORE DATABASE` som läser bak-filen kan ständigt nya försök för att läsa bak-filen och returnerar fel efter lång tidsperiod om signatur för delad åtkomst i `CREDENTIAL` är felaktig. Köra RESTORE HEADERONLY innan du återställer en databas för att se till att SAS-nyckel är korrekt.
Kontrollera att du tar bort ledande `?` från SAS-nyckeln som genereras med hjälp av Azure portal.

### <a name="tooling"></a>Verktyg

SQL Server Management Studio (SSMS) och SQL Server Data Tools (SSDT) kan ha några problem vid åtkomst till en hanterad instans.

- Med hjälp av Azure AD-server-huvudkonton (inloggningar) och användare (**förhandsversion**) med SSDT stöds inte för tillfället.
- Skript för Azure AD-server-huvudkonton (inloggningar) och användare (**förhandsversion**) stöds inte i SSMS.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Felaktig databasnamn i vissa vyer, loggar och meddelanden

Visa GUID databasidentifierare i stället för faktiska databasnamn flera systemvyer, prestandaräknare, felmeddelanden, XEvents och poster i felloggen. Förlita dig inte på dessa GUID-identifierare eftersom de skulle ersättas med faktiska databasnamn i framtiden.

### <a name="database-mail"></a>Database-mail

`@query` parametern i [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) proceduren fungerar inte.

### <a name="database-mail-profile"></a>Database mail-profilen

Database mail-profilen som används av SQL-agenten måste anropas `AzureManagedInstance_dbmail_profile`. Det finns ingen begränsning avseende andra databasnamn e-profilen.

### <a name="error-logs-are-not-persisted"></a>Felloggar är inte beständiga

Felloggarna som är tillgängliga i Managed Instance är inte beständiga och deras storlek ingår inte i den maximala lagringsgränsen. Felloggarna kan raderas automatiskt vid redundans.

### <a name="error-logs-are-verbose"></a>Felloggar finns utförlig

En hanterad instans placerar utförlig information i felloggarna och många av dem är inte relevanta. Att kommer minska mängden information i felloggarna i framtiden.

**Lösning**: Använd en anpassad procedur för att läsa felloggar som filter ut vissa icke-relevanta poster. Mer information finns i [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transaktions-Scope på två databaser inom samma instans stöds inte

`TransactionScope` klassen i .NET fungerar inte om två frågor skickas till de två databaserna inom samma instans under samma transaktionsomfattning:

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

**Lösning**: Använd [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) använda andra database i kontext och anslutning istället för att använda två anslutningar.

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>CLR-moduler och en stund länkade servrar kan inte referera till lokal IP-adress

CLR-moduler som placeras i en hanterad instans och länkade servrar/distribuerade frågor som refererar till aktuell instans någon gång det går inte att matcha IP-Adressen för den lokala instansen. Det här felet är ett övergående problem.

**Lösning**: Använd om möjligt kontext anslutningar i CLR-modulen.

### <a name="tde-encrypted-databases-with-service-managed-key-dont-support-user-initiated-backups"></a>Transparent Datakryptering krypteras databaser med tjänsthanterad nyckel stöder inte användarinitierad säkerhetskopiering

Du kan inte utföra `BACKUP DATABASE ... WITH COPY_ONLY` för en databas som är krypterad med tjänsthanterad Transparent datakryptering (TDE). Tjänsthanterad TDE tvingar säkerhetskopieringar krypteras med internt TDE-nyckel och nyckeln kan inte exporteras, så du kan inte att återställa säkerhetskopian.

**Lösning**: Använd automatisk säkerhetskopiering och point-in-time-återställning, eller Använd [kundhanterad (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) i stället, eller inaktivera kryptering på databasen.

## <a name="next-steps"></a>Nästa steg

- Mer information om hanterade instanser finns [vad är en hanterad instans?](sql-database-managed-instance.md)
- För en funktioner och jämförelse lista, se [SQL vanliga funktioner](sql-database-features.md).
- En Snabbstart som visar hur du skapar en ny hanterad instans, se [skapar en hanterad instans](sql-database-managed-instance-get-started.md).
