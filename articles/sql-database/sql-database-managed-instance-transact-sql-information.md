---
title: Azure SQL Database Managed Instance T-SQL skillnader | Microsoft Docs
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
ms.openlocfilehash: 08920a25fc7213a773ef0d76a5daddbab3f765c2
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866868"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database Managed Instance T-SQL skillnader från SQL Server

Den här artikeln sammanfattar och förklarar skillnader i syntaxen och beteende mellan Azure SQL Database Managed Instance och en lokal SQL Server Database Engine. Följande ämnen diskuteras: <a name="Differences"></a>

- [Tillgänglighet](#availability) innehåller skillnader i [alltid på](#always-on-availability) och [säkerhetskopior](#backup).
- [Security](#security) innehåller skillnader i [granskning](#auditing), [certifikat](#certificates), [autentiseringsuppgifter](#credential), [kryptografiproviders](#cryptographic-providers), [inloggningar och användare](#logins-and-users), och [tjänstnyckeln och tjänstens huvudnyckel](#service-key-and-service-master-key).
- [Konfigurationen](#configuration) innehåller skillnader i [buffra pool tillägget](#buffer-pool-extension), [sortering](#collation), [kompatibilitetsnivå](#compatibility-levels), [databasspegling ](#database-mirroring), [databasalternativ](#database-options), [SQL Server Agent](#sql-server-agent), och [Tabellalternativ](#tables).
- [Lär dig om funktionerna](#functionalities) innehåller [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuerade transaktioner](#distributed-transactions), [utökade händelser](#extended-events), [externa bibliotek](#external-libraries), [filestream- och FileTable](#filestream-and-filetable), [semantiska fulltextsökning](#full-text-semantic-search), [länkade servrar](#linked-servers), [PolyBase](#polybase), [replikering](#replication), [ÅTERSTÄLLA](#restore-statement), [Service Broker](#service-broker), [lagrade procedurer, funktioner och utlösare](#stored-procedures-functions-and-triggers).
- [Funktioner som har olika beteenden i hanterade instanser](#Changes).
- [Temporära begränsningar och kända problem](#Issues).

Alternativ för distribution av Managed Instance tillhandahåller hög kompatibilitet med en lokal SQL Server Database Engine. De flesta av SQL Server database engine-funktioner stöds i en hanterad instans.

![Migrering](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Tillgänglighet

### <a name="always-on-availability"></a>Alltid på

[Hög tillgänglighet](sql-database-high-availability.md) är inbyggd i Managed Instance och kan inte styras av användare. Följande uttryck stöds inte:

- [SKAPA SLUTPUNKT... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [SKAPA TILLGÄNGLIGHETSGRUPP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Den [ange HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) -satsen i den [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) instruktionen

### <a name="backup"></a>Backup

Hanterade instanser har automatisk säkerhetskopiering så att användare kan skapa fullständiga databasen `COPY_ONLY` säkerhetskopior. Differentiell, logg och av filögonblicksbilder stöds inte.

- Med en hanterad instans kan du säkerhetskopiera en databasinstans endast till en Azure Blob storage-konto:
  - Endast `BACKUP TO URL` stöds.
  - `FILE`, `TAPE`, och enheter för säkerhetskopiering stöds inte.
- De flesta av allmänna `WITH` alternativ stöds.
  - `COPY_ONLY` är obligatoriskt.
  - `FILE_SNAPSHOT` stöds inte.
  - Bandalternativ: `REWIND`, `NOREWIND`, `UNLOAD`, och `NOUNLOAD` stöds inte.
  - Log-specifika alternativ: `NORECOVERY`, `STANDBY`, och `NO_TRUNCATE` stöds inte.

Begränsningar: 

- Med en hanterad instans, du kan säkerhetskopiera en databasinstans till en säkerhetskopia med upp till 32 stripe, vilket räcker för databaser upp till 4 TB om komprimering av säkerhetskopior används.
- Maximal säkerhetskopiering stripe-storlek med hjälp av den `BACKUP` kommandot i en hanterad instans är 195 GB, vilket är den maximala blobstorleken. Öka antalet stripe i backup kommandot för att minska Stripestorleken på enskilda och stannar inom den här gränsen.

    > [!TIP]
    > För att undvika denna begränsning, när du säkerhetskopierar en databas från antingen SQL Server i en lokal miljö eller i en virtuell dator, kan du:
    >
    > - Säkerhetskopiera till `DISK` i stället för att säkerhetskopiera till `URL`.
    > - Ladda upp de säkerhetskopiera filerna till Blob storage.
    > - Återställa till den hanterade instansen.
    >
    > Den `Restore` kommandot i en hanterad instans stöder större storlekar för blob i de säkerhetskopiera filerna eftersom en annan blob-typ används för lagring av överförda säkerhetskopian.

Information om säkerhetskopior med hjälp av T-SQL finns i [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Säkerhet

### <a name="auditing"></a>Granskning

De viktigaste skillnaderna mellan granskning i databaser i Azure SQL Database och databaser i SQL Server är:

- Med alternativet för hanterad instans-distribution i Azure SQL Database-granskning fungerar på servernivå. Den `.xel` loggfilerna lagras i Azure Blob storage.
- Med enkel databas och elastisk pool distributionsalternativ i Azure SQL Database-granskning fungerar på databasnivå.
- Granskning fungerar på servernivå i SQL Server på lokala eller virtuella datorer. Händelser som lagras på filsystemet eller Windows-händelseloggar.
 
XEvent granskning i Managed Instance stöder prestandamål i Azure Blob storage. Fil- och Windows-loggar stöds inte.

Nyckeln skillnader i den `CREATE AUDIT` syntax för granskning till Azure Blob storage är:

- En ny syntax `TO URL` är förutsatt att du kan använda för att ange Webbadressen till Azure Blob storage-behållare där de `.xel` filerna är placerade.
- Syntaxen `TO FILE` stöds inte eftersom en hanterad instans inte kan komma åt Windows-filresurser.

Mer information finns i: 

- [SKAPA SERVERGRANSKNING](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikat

En hanterad instans kan inte komma åt delade filer och mappar i Windows, så gäller följande begränsningar:

- Den `CREATE FROM` / `BACKUP TO` filen stöds inte för certifikat.
- Den `CREATE` / `BACKUP` certifikat från `FILE` / `ASSEMBLY` stöds inte. Privata nyckelfilerna kan inte användas. 

Se [skapa certifikat](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) och [säkerhetskopiera certifikat](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Lösning**: Skript för certifikatet eller den privata nyckeln, lagra som SQL-filen och skapa från binär:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Autentiseringsuppgift

Endast Azure Key Vault och `SHARED ACCESS SIGNATURE` identiteter stöds. Windows-användare stöds inte.

Se [skapa AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) och [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kryptografiproviders

En hanterad instans kan inte komma åt filer, så det inte går att skapa kryptografiproviders:

- `CREATE CRYPTOGRAPHIC PROVIDER` stöds inte. Se [skapa KRYPTOGRAFIPROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` stöds inte. Se [ALTER KRYPTOGRAFIPROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Inloggningar och användare

- SQL-inloggningar som skapats med hjälp av `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, och `FROM SID` stöds. Se [skapa inloggningen](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD)-server-huvudkonton (inloggningar) skapas med den [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) syntax eller [skapa från ANVÄNDARINLOGGNING [Azure AD-kontoinloggning]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) syntax som stöds (offentlig förhandsversion). Dessa inloggningar skapas på servernivå.

    Hanterad instans har stöd för Azure AD-huvudkonton med syntaxen `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Den här funktionen kallas även för Azure AD finns databasanvändare.

- Windows-inloggningar som skapats med den `CREATE LOGIN ... FROM WINDOWS` syntaxen stöds inte. Använd Azure Active Directory-inloggningar och användare.
- Azure AD-användaren som skapade instansen har [obegränsad administratörsprivilegier](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Inte Azure AD på databasnivå är administratör kan skapas med hjälp av den `CREATE USER ... FROM EXTERNAL PROVIDER` syntax. Se [skapa användare... FRÅN EXTERN PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Azure AD-server-huvudkonton (inloggningar) stöder SQL-funktioner i endast en hanterad instans. Funktioner som kräver flera instans interaktion, oavsett om de är inom samma Azure AD-klient eller olika klienter stöds inte för Azure AD-användare. Exempel på sådana funktioner är:

  - Transaktionsreplikering i SQL.
  - Länkserver.

- Ange en Azure AD-inloggningen som mappades till en Azure AD-grupp som databasens ägare inte stöds.
- Personifiering av Azure AD-huvudkonton på servernivå med hjälp av andra Azure AD-huvudkonton stöds, till exempel den [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) satsen. En EXECUTE AS-begränsningarna är:

  - EXECUTE AS USER stöds inte för Azure AD-användare när namnet skiljer sig från inloggningsnamnet. Ett exempel är när användaren har skapats via syntax skapa [myAadUser] från ANVÄNDARINLOGGNING [john@contoso.com] och personifiering görs via EXEC AS USER = _myAadUser_. När du skapar en **användaren** från en server huvudnamn för Azure AD (inloggning), ange användarnamn som samma login_name från **inloggning**.
  - Endast SQL-servernivå huvudkontona (inloggningar) som ingår i den `sysadmin` rollen kan utföra följande åtgärder som är riktade till Azure AD-huvudkonton:

    - KÖRA SOM ANVÄNDARE
    - KÖRA SOM INLOGGNING

- Offentlig förhandsversionsbegränsningar för Azure AD-server-huvudkonton (inloggningar):

  - Active Directory-administratören begränsningar för hanterad instans:

    - Azure AD-administratören som används för att ställa in den hanterade instansen kan inte användas för att skapa en Azure AD-server principal (inloggning) i den hanterade instansen. Du måste skapa den första Azure AD serverhuvudobjekt (inloggning) med hjälp av en SQL Server-tjänstkontot som är en `sysadmin` roll. Den här tillfälliga begränsningen tas bort när Azure AD-server-huvudkonton (inloggningar) blir allmänt tillgänglig. Om du försöker använda en Azure AD-administratörskonto för att skapa inloggningen, se följande fel: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - För närvarande är den första Azure AD-kontoinloggning som skapats i master-databasen måste ha skapats av det SQL Server-kontot av standardtyp (icke-Azure AD) är en `sysadmin` rollen med hjälp av [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) från extern PROVIDER. Den här begränsningen tas bort efter allmän tillgänglighet. Därefter kan du skapa en inledande Azure AD-kontoinloggning med hjälp av Active Directory-administratör för hanterad instans.
    - DacFx (export/import) användas med SQL Server Management Studio eller SqlPackage stöds inte för Azure AD-inloggningar. Den här begränsningen tas bort när Azure AD-server-huvudkonton (inloggningar) blir allmänt tillgänglig.
    - Med hjälp av Azure AD-server-huvudkonton (inloggningar) med SQL Server Management Studio:

      - Skript Azure AD-inloggningar som använder valfri autentiserad inloggning stöds inte.
      - IntelliSense kan identifiera inte skapa inloggning från en extern PROVIDER-instruktionen och visar en röd understrykning.

- Endast den huvudnamn inloggning på servernivå, som skapas av den hanterade instansen etableringsprocessen medlemmar av serverroller, till exempel `securityadmin` eller `sysadmin`, eller andra inloggningar med ALTER ANY LOGIN-behörighet på servernivå kan skapa Azure AD Server-huvudkonton (inloggningar) i master-databasen för hanterad instans.
- Om inloggningen är ett huvudnamn för SQL, endast inloggningar som ingår i den `sysadmin` rollen kan använda kommandot Skapa för att skapa inloggningar för en Azure AD-konto.
- Azure AD-kontoinloggning måste vara medlem i en Azure AD i samma katalog som används för Azure SQL Database Managed Instance.
- Azure AD-server-huvudkonton (inloggningar) är synliga i Object Explorer från och med SQL Server Management Studio 18.0 preview 5.
- Överlappande Azure AD-huvudkonton server (inloggningar) med ett administratörskonto för Azure AD är tillåtet. Azure AD-server-huvudkonton (inloggningar) högre prioritet än Azure AD-administratören när du lösa huvudkontot och tillämpa behörigheter för den hanterade instansen.
- Under autentiseringen tillämpas följande sekvens för att lösa det autentiserande huvudnamnet:

    1. Om Azure AD-kontot finns ha direkt mappad till Azure AD tjänstens huvudnamn (inloggning), som finns i sys.server_principals som typen ”E”, bevilja åtkomst och tillämpa behörigheter för Azure AD tjänstens huvudnamn (inloggning).
    2. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till Azure AD tjänstens huvudnamn (inloggning), som finns i sys.server_principals som skriver ”X”, bevilja åtkomst och tillämpa behörigheter för inloggningen för Azure AD-grupp.
    3. Om Azure AD-kontot är en särskild portal konfigurerade gäller Azure AD-administratör för hanterad instans, som inte finns i systemvyer för hanterad instans, särskilt fast behörigheterna för Azure AD-administratör för hanterad instans (bakåtkompatibelt läge).
    4. Om Azure AD-kontot finns som direkt mappade till Azure AD-användare i en databas, som finns i sys.database_principals som typen ”E”, bevilja åtkomst och tillämpa behörigheter för Azure AD-databasanvändare.
    5. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till en Azure AD-användare i en databas, som finns i sys.database_principals som typen ”X”, bevilja åtkomst och tillämpa behörigheter för inloggningen för Azure AD-grupp.
    6. Om det finns en Azure AD-inloggning som mappats till en Azure AD-användarkonto eller ett gruppkonto för Azure AD som motsvarar den användare som autentiseras tillämpas alla behörigheter från den här Azure AD-inloggningen.

### <a name="service-key-and-service-master-key"></a>Tjänsten nycklar och tjänstens huvudnyckel

- [Huvudnyckeln för säkerhetskopiering](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten).
- [Återställ huvudnyckeln](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten).
- [Tjänstens huvudnyckel backup](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten).
- [Tjänstens huvudnyckel återställning](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten).

## <a name="configuration"></a>Konfiguration

### <a name="buffer-pool-extension"></a>Buffertpooltillägget

- [Buffra pool tillägget](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) stöds inte.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` stöds inte. Se [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortering

Standardsortering för instansen är `SQL_Latin1_General_CP1_CI_AS` och kan anges som en parameter för skapandet. Se [sorteringar](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Efterlevnadsnivåer

- Stöds kompatibilitetsnivå är 100, 110, 120, 130 och 140.
- Efterlevnadsnivåer under 100 stöds inte.
- Standardkompatibilitetsnivån för nya databaser är 140. Kompatibilitetsnivå förblir oförändrat för återställda databaser, om den var 100 och högre.

Se [ALTER Databaskompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Databasspegling

Databasspegling stöds inte.

- `ALTER DATABASE SET PARTNER` och `SET WITNESS` alternativ stöds inte.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` stöds inte.

Mer information finns i [ALTER DATABASE SET PARTNER och SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) och [CREATE ENDPOINT... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Databasalternativ

- Flera loggfiler stöds inte.
- InMemory-objekt stöds inte på tjänstnivån generell användning. 
- Det finns en gräns på 280 filer per instans generell användning, vilket innebär upp till 280 filer per databas. Både data och loggfiler filer i nivån generell användning räknas mot den här gränsen. [Stöder 32 767 filer per databas på nivån affärskritisk](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Databasen får inte innehålla filgrupper som innehåller filestream-data. Återställningen misslyckas om .bak innehåller `FILESTREAM` data. 
- Varje fil placeras i Azure Blob storage. I/o och dataflöde per fil beror på storleken på varje enskild fil.

#### <a name="create-database-statement"></a>CREATE DATABASE-instruktionen

Följande begränsningar gäller för `CREATE DATABASE`:

- Filer och filgrupper kan inte definieras. 
- Den `CONTAINMENT` alternativet stöds inte. 
- `WITH` alternativ stöds inte. 
   > [!TIP]
   > Som en tillfällig lösning kan du använda `ALTER DATABASE` när `CREATE DATABASE` ange databasalternativ att lägga till filer eller ange inneslutning. 

- Den `FOR ATTACH` alternativet stöds inte.
- Den `AS SNAPSHOT OF` alternativet stöds inte.

Mer information finns i [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instruktionen ALTER DATABASE

Vissa egenskaper för filer kan inte ange eller ändra:

- En sökväg till filen kan inte anges i den `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-instruktionen. Ta bort `FILENAME` från skriptet eftersom automatiskt placerar filer i en hanterad instans. 
- Ett filnamn kan inte ändras med hjälp av den `ALTER DATABASE` instruktionen.

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

- SQL Server Agent-inställningarna är skrivskyddade. Proceduren `sp_set_agent_properties` stöds inte i hanterade instanser. 
- Jobb
  - Steg för T-SQL-jobb stöds.
  - Följande replikeringsjobb stöds:
    - Transaktionsloggen läsare
    - Ögonblicksbild
    - Distributören
  - Steg för SSIS-jobb stöds.
  - Andra typer av jobb steg stöds inte för närvarande:
    - Jobbsteg för sammanslagen replikering stöds inte. 
    - Kön läsare stöds inte. 
    - Kommandogränssnitt (shell) stöds inte ännu.
  - Hanterade instanser kan inte komma åt externa resurser, till exempel nätverksresurser via robocopy. 
  - SQL Server Analysis Services stöds inte.
- Meddelanden stöds delvis.
- E-postmeddelande stöds, även om det krävs att du konfigurerar en Database Mail-profil. SQL Server Agent kan använda endast en Database Mail-profilen och den måste anropas `AzureManagedInstance_dbmail_profile`. 
  - Personsökare stöds inte. 
  - NetSend stöds inte.
  - Aviseringar stöds inte ännu.
  - Proxyservrar stöds inte. 
- EventLog stöds inte.

Följande funktioner stöds inte för närvarande, men kommer att aktiveras i framtiden:

- Proxyservrar
- Schemalägga jobb på en inaktiv CPU
- Aktivera eller inaktivera en Agent
- Aviseringar

Information om SQL Server Agent finns i [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabeller

Följande tabeller stöds inte:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Information om hur du skapar och ändra tabeller finns i [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) och [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funktioner

### <a name="bulk-insert--openrowset"></a>Massinfogning / openrowset

En hanterad instans kan inte komma åt delade filer och mappar i Windows, så att filerna måste importeras från Azure Blob storage:

- `DATASOURCE` Du måste ange i den `BULK INSERT` kommandot medan du importerar filer från Azure Blob storage. Se [MASSINFOGNING](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` Du måste ange i den `OPENROWSET` fungerar när du läsa innehållet i en fil från Azure Blob storage. Se [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

En hanterad instans kan inte komma åt delade filer och mappar i Windows, så gäller följande begränsningar:

- Endast `CREATE ASSEMBLY FROM BINARY` stöds. Se [skapa sammansättningen från binär](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` stöds inte. Se [skapa sammansättningen från filen](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` Det går inte att referera till filer. Se [ändring av sammansättningen](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Odokumenterade DBCC-uttryck som är aktiverade i SQL Server stöds inte i hanterade instanser.

- `Trace flags` stöds inte. Se [Spårningsflaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` stöds inte. Se [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` stöds inte. Se [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Distribuerade transaktioner

MSDTC och [elastiska transaktioner](sql-database-elastic-transactions-overview.md) för närvarande stöds inte i hanterade instanser.

### <a name="extended-events"></a>Extended Events

Vissa Windows-specifika mål för utökade händelser (XEvents) stöds inte:

- Den `etw_classic_sync` stöds inte target. Store `.xel` filer i Azure Blob storage. Se [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Den `event_file` stöds inte target. Store `.xel` filer i Azure Blob storage. Se [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externa bibliotek

I databasen R och Python, externa bibliotek är ännu inte stöds. Se [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream och FileTable

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

- Mål som stöds är SQL Server och SQL-databas.
- Mål som inte stöds är filer, Analysis Services och andra RDBMS.

Åtgärder

- Cross-instans skrivtransaktioner stöds inte.
- `sp_dropserver` har stöd för att släppa en länkad server. See [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Den `OPENROWSET` funktionen kan användas för att köra frågor endast på SQL Server-instanser. De kan vara antingen hanterade, lokalt eller i virtuella datorer. Se [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- Den `OPENDATASOURCE` funktionen kan användas för att köra frågor endast på SQL Server-instanser. De kan vara antingen hanterade, lokalt eller i virtuella datorer. Endast den `SQLNCLI`, `SQLNCLI11`, och `SQLOLEDB` värden som stöds som en provider. Ett exempel är `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Se [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Externa tabeller som refererar filer i HDFS- eller Azure Blob-lagring inte stöds. Läs om hur PolyBase [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikering

Replikering är tillgänglig för en förhandsversion för hanterad instans. Information om replikering finns i [SQL Server-replikering](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>ÅTERSTÄLLA instruktionen 

- Syntax som stöds:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Syntax som inte stöds:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Källa: 
  - `FROM URL` (Azure Blob storage) är det enda alternativet som stöds.
  - `FROM DISK`/`TAPE`/ enhet för säkerhetskopiering stöds inte.
  - Säkerhetskopior stöds inte.
- `WITH` alternativ stöds inte, till exempel ingen `DIFFERENTIAL` eller `STATS`.
- `ASYNC RESTORE`: Återställningen fortsätter även om klientanslutningen bryts. Om anslutningen bryts, du kan kontrollera den `sys.dm_operation_status` vyn för status för återställning och för att skapa och släpp-databasen. Se [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Följande databasalternativ ställs in eller åsidosätts och kan inte ändras senare: 

- `NEW_BROKER` Om den asynkrona meddelandekön inte är aktiverad i bak-filen. 
- `ENABLE_BROKER` Om den asynkrona meddelandekön inte är aktiverad i bak-filen. 
- `AUTO_CLOSE=OFF` Om en databas i bak-filen har `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Om en databas i bak-filen har `SIMPLE` eller `BULK_LOGGED` återställningsläge.
- Minnesoptimerade filgrupper läggs och kallas XTP om den inte i källan bak-filen. 
- Alla befintliga minnesoptimerade filgrupper har bytt namn till XTP. 
- `SINGLE_USER` och `RESTRICTED_USER` alternativ konverteras till `MULTI_USER`.

Begränsningar: 

- `.BAK` filer som innehåller flera säkerhetskopior kan inte återställas. 
- `.BAK` filer som innehåller flera loggfiler kan inte återställas.
- Återställningen misslyckas om .bak innehåller `FILESTREAM` data.
- Säkerhetskopieringar som innehåller databaser som har aktiva InMemory-objekt kan inte återställas på en instans för generell användning. Information om återställning instruktioner finns i [RESTORE-uttryck](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Cross-instans service broker stöds inte:

- `sys.routes`: Du måste välja adressen från sys.routes som ett krav. Adressen måste vara lokal på varje väg. Se [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Du kan inte använda `CREATE ROUTE` med `ADDRESS` än `LOCAL`. Se [skapa väg](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Du kan inte använda `ALTER ROUTE` med `ADDRESS` än `LOCAL`. Se [ALTER väg](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Lagrade procedurer, funktioner och utlösare

- `NATIVE_COMPILATION` stöds inte i nivån generell användning.
- Följande [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) alternativ stöds inte: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` stöds inte. Se [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` stöds inte. Se [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` stöds inte, vilket inkluderar `sp_addextendedproc`  och `sp_dropextendedproc`. Se [utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`, och `sp_detach_db` stöds inte. Se [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), och [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Funktionalitetsförändringar

Följande variabler, uppgifter och vyer returnerar olika resultat:

- `SERVERPROPERTY('EngineEdition')` Returnerar värdet 8. Den här egenskapen identifierar en hanterad instans. Se [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Returnerar NULL eftersom begreppet instans som det finns för SQL Server inte gäller för en hanterad instans. Se [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Returnerar ett fullständigt DNS-”läge för anslutningsbart” namn, till exempel mitt-managed-instance.wcus17662feb9ce98.database.windows.net. Se [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Returnerar ett fullständigt DNS-”läge för anslutningsbart” namn, till exempel `myinstance.domain.database.windows.net` för egenskaperna ”name” och ”data_source”. Se [SYS. SERVRAR](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Returnerar NULL eftersom begreppet tjänsten eftersom det finns för SQL Server inte gäller för en hanterad instans. Se [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` stöds. Den returnerar NULL om det inte finns i sys.syslogins Azure AD-kontoinloggning. Se [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` stöds inte. Felaktiga data returneras, vilket är en tillfällig känt problem. Se [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Kända problem och begränsningar

### <a name="tempdb-size"></a>TEMPDB-storlek

Den maximala filstorleken för `tempdb` får inte överskrida 24 GB per kärna på en allmän-nivån. Maximalt `tempdb` storleken på en affärskritisk nivå är begränsad med lagringsstorlek instans. Den `tempdb` databasen alltid upp till 12 datafiler. Den här största storleken per fil inte kan ändras och nya filer som kan läggas till `tempdb`. Några frågor kan returnera ett fel om de behöver mer än 24 GB per kärna i `tempdb`.

### <a name="cant-restore-contained-database"></a>Det går inte att återställa innesluten databas

Hanterad instans kan inte återställa [inneslutna databaser](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Point-in-time-återställning av befintliga inneslutna databaser fungerar inte på hanterad instans. Det här problemet löses snart. Under tiden kan rekommenderar vi att du tar bort inneslutningsalternativet från databaser som är placerade på hanterad instans. Använd inte inneslutningsalternativet för produktionsdatabaserna. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Överstiger lagringsutrymme med små databasfiler

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, och `RESTORE DATABASE` instruktioner misslyckas, eftersom instansen kan uppnår sin gräns för Azure Storage.

Varje hanterad instans generell användning har upp till 35 TB lagringsutrymme som är reserverade för Azure Premium diskutrymme. Varje databasfil placeras på en separat fysisk disk. Diskstorlekar kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Outnyttjat utrymme på disken är inte debiteras dock totalsumman för Azure Premium-diskstorlekar får inte överskrida 35 TB. I vissa fall kanske en hanterad instans som inte behöver 8 TB totalt överskrider 35 TB Azure begränsa lagringsstorlek på grund av interna fragmentering.

En hanterad instans generell användning kan till exempel ha en fil som är 1,2 TB i storlek som placeras på en disk med 4 TB. Det kan också ha 248 filer som är varje 1 GB i storlek som är placerade på separata 128 GB-diskar. I det här exemplet:

- Den totala allokerade disk lagringsstorleken är 1 x 4 TB + 248 x 128 GB = 35 TB.
- Det totala reserverade utrymmet för databaser på instansen är 1 x 1.2 TB + 248 x 1 GB = 1,4 TB.

Det här exemplet visar att i vissa fall, på grund av en specifik distribution av filer, en hanterad instans kan uppnår sin gräns för 35 TB som är reserverat för en ansluten Azure Premium Disk när du kanske inte den ska.

I det här exemplet befintliga databaser fortsätta att fungera och kan växa utan problem, förutsatt att nya filer inte har lagts till. Nya databaser kan inte skapas eller återställas eftersom det inte finns tillräckligt med utrymme för nya diskenheter, även om den totala storleken på alla databaser inte nå storleksgränsen för instansen. Felet som returnerades är i så fall inte rensa.

Du kan [identifiera antalet återstående filer](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) med hjälp av systemvyer. Om du når den här gränsen kan du prova att [tom och ta bort några av de mindre filerna med hjälp av instruktionen DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) eller växla till den [affärskritisk nivå, som inte har den här gränsen](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Felaktig konfiguration av SAS-nyckeln under databasen återställa

`RESTORE DATABASE` att läsningar bak-filen kan ständigt nya försök för att läsa kommer bak filen och returnerar ett fel när du har en lång tidsperiod om signatur för delad åtkomst i `CREDENTIAL` är felaktig. Köra RESTORE HEADERONLY innan du återställer en databas för att se till att SAS-nyckeln är korrekt.
Kontrollera att du tar bort ledande `?` från SAS-nyckeln som genereras med hjälp av Azure portal.

### <a name="tooling"></a>Verktyg

SQL Server Management Studio och SQL Server Data Tools kan ha några problem när de har åtkomst till en hanterad instans.

- Med Azure AD-server-huvudkonton (inloggningar) och användare (offentlig förhandsversion) med SQL Server Data Tools för närvarande stöds inte.
- Skript för Azure AD-server-huvudkonton (inloggningar) och användare (offentlig förhandsversion) stöds inte i SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Felaktig databasnamn i vissa vyer, loggar och meddelanden

Visa GUID databasidentifierare i stället för faktiska databasnamn flera systemvyer, prestandaräknare, felmeddelanden, XEvents och poster i felloggen. Använd inte dessa GUID-identifierare eftersom de har ersatts med faktiska databasnamn i framtiden.

### <a name="database-mail"></a>Database-mail

Den `@query` parametern i den [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) proceduren inte fungerar.

### <a name="database-mail-profile"></a>Database Mail-profilen

Database Mail-profil som används av SQL Server Agent måste anropas `AzureManagedInstance_dbmail_profile`. Det finns inga begränsningar för andra namn för Database Mail-profilen.

### <a name="error-logs-arent-persisted"></a>Felloggar sparas inte

Felloggarna som är tillgängliga i Managed Instance är inte beständiga och deras storlek ingår inte i det maximala lagringsutrymmet. Felloggarna kan tas bort automatiskt om redundansväxlingen.

### <a name="error-logs-are-verbose"></a>Felloggar finns utförlig

En hanterad instans placerar utförlig information i felloggarna och mycket av det är inte relevant. Mängden information i felloggarna sänks i framtiden.

**Lösning:** Använda en anpassad procedur för att läsa felloggar som filtrerar ut några irrelevanta poster. Mer information finns i [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transaktions-scope på två databaser inom samma instans stöds inte

Den `TransactionScope` klassen i .NET fungerar inte om två frågor skickas till två databaser inom samma instans under samma transaktionsomfattning:

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

Även om den här koden fungerar med data i samma instans, krävs MSDTC.

**Lösning:** Använd [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) att använda en annan databas i en kontext och anslutning istället för att använda två anslutningar.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-moduler och ibland länkade servrar kan inte referera till en lokal IP-adress

CLR-moduler som placeras i en hanterad instans och länkade servrar eller distribuerade frågor som refererar till en aktuell instans ibland inte kan lösa IP-Adressen för en lokal instans. Det här felet är ett övergående problem.

**Lösning:** Använd om möjligt kontext anslutningar i en CLR-modul.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>TDE-krypterade databaser med en tjänsthanterad nyckel stöder inte användarinitierad säkerhetskopiering

Du kan inte utföra `BACKUP DATABASE ... WITH COPY_ONLY` för en databas som är krypterad med tjänsthanterad Transparent datakryptering (TDE). Tjänsthanterad TDE tvingar säkerhetskopieringar krypteras med en intern TDE-nyckel. Nyckeln kan inte exporteras så att du inte kan återställa säkerhetskopian.

**Lösning:** Använd automatisk säkerhetskopiering och point-in-time-återställning, eller Använd [kundhanterad (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) i stället. Du kan också inaktivera kryptering på databasen.

## <a name="next-steps"></a>Nästa steg

- Mer information om hanterade instanser finns i [vad är en hanterad instans?](sql-database-managed-instance.md)
- För en funktioner och jämförelse lista, se [Azure SQL Database funktionsjämförelse](sql-database-features.md).
- En Snabbstart som visar hur du skapar en ny hanterad instans, se [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
