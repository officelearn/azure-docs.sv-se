---
title: Azure SQL Database hanterade instans T-SQL-skillnader | Microsoft Docs
description: I den här artikeln beskrivs skillnader i T-SQL mellan en hanterad instans i Azure SQL Database och SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 08/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9a043d07004870c00c656b655d56a1526f8993d8
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2019
ms.locfileid: "72000492"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>Hanterade instans T-SQL-skillnader, begränsningar och kända problem

Den här artikeln sammanfattar och förklarar skillnaderna i syntax och beteende mellan Azure SQL Database Hanterad instans och lokala SQL Server databas motor. Distributions alternativet för hanterade instanser ger hög kompatibilitet med lokala SQL Server databas motor. De flesta av funktionerna i SQL Server-databasmotorn stöds i en hanterad instans.

![Migrering](./media/sql-database-managed-instance/migration.png)

Det finns vissa PaaS-begränsningar som introduceras i den hanterade instansen och vissa beteende ändringar jämfört med SQL Server. Skillnaderna är indelade i följande kategorier:<a name="Differences"></a>

- [Tillgänglighet](#availability) inkluderar skillnaderna i [Always on](#always-on-availability) och [backups](#backup).
- [Säkerhet](#security) omfattar skillnaderna i [granskning](#auditing), [certifikat](#certificates), [autentiseringsuppgifter](#credential), [kryptografiproviders](#cryptographic-providers), [inloggningar och användare](#logins-and-users)samt [tjänst nyckeln och tjänstens huvud nyckel](#service-key-and-service-master-key).
- [Konfigurationen](#configuration) inkluderar skillnaderna i [tillägg för buffertpooltillägget](#buffer-pool-extension), [sortering](#collation), [kompatibilitetsnivå](#compatibility-levels), [databas spegling](#database-mirroring), [databas alternativ](#database-options), [SQL Server Agent](#sql-server-agent)och [tabell alternativ](#tables).
- [Funktionerna](#functionalities) omfattar [bulk INSERT/OpenRowSet](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuerade transaktioner](#distributed-transactions), [utökade händelser](#extended-events), [externa bibliotek](#external-libraries), [FILESTREAM och FileTable](#filestream-and-filetable), [full text Semantisk sökning](#full-text-semantic-search), [länkade servrar](#linked-servers), [PolyBase](#polybase), [replikering](#replication), [återställning](#restore-statement), [Service Broker](#service-broker), [lagrade procedurer, funktioner och utlösare](#stored-procedures-functions-and-triggers).
- [Miljö inställningar](#Environment) som virtuella nätverk och under näts konfiguration.

De flesta av dessa funktioner är arkitektur begränsningar och representerar tjänst funktioner.

Den här sidan förklarar även [tillfälliga kända problem](#Issues) som upptäcks i en hanterad instans, vilket kommer att lösas i framtiden.

## <a name="availability"></a>Tillgänglighet

### <a name="always-on-availability"></a>Always on

[Hög tillgänglighet](sql-database-high-availability.md) är inbyggt i en hanterad instans och kan inte styras av användare. Följande uttryck stöds inte:

- [SKAPA SLUT PUNKT... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [SKAPA TILLGÄNGLIGHETS GRUPP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ÄNDRA TILLGÄNGLIGHETS GRUPP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [SLÄPP TILLGÄNGLIGHETS GRUPP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [Set hadr](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) -satsen i [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) -instruktionen

### <a name="backup"></a>Säkerhetskopiera

Hanterade instanser har automatiska säkerhets kopieringar, så användare kan skapa fullständiga databas `COPY_ONLY` säkerhets kopior. Säkerhets kopiering av differentiella, loggade och ögonblicks bilder stöds inte.

- Med en hanterad instans kan du bara säkerhetskopiera en instans databas till ett Azure Blob Storage-konto:
  - Endast `BACKUP TO URL` stöds.
  - `FILE`, `TAPE`-och säkerhets kopierings enheter stöds inte.
- De flesta allmänna `WITH`-alternativen stöds.
  - `COPY_ONLY` är obligatoriskt.
  - `FILE_SNAPSHOT` stöds inte.
  - Band alternativ: `REWIND`, `NOREWIND`, `UNLOAD` och `NOUNLOAD` stöds inte.
  - Användarspecifika alternativ: `NORECOVERY`, `STANDBY` och `NO_TRUNCATE` stöds inte.

Begränsningar: 

- Med en hanterad instans kan du säkerhetskopiera en instans databas till en säkerhets kopia med upp till 32 Stripes, vilket är tillräckligt för databaser upp till 4 TB om komprimering av säkerhets kopia används.
- Du kan inte köra `BACKUP DATABASE ... WITH COPY_ONLY` på en databas som är krypterad med tjänst hanterad transparent datakryptering (TDE). Service-Managed TDE tvingar säkerhets kopieringarna att krypteras med en intern TDE-nyckel. Det går inte att exportera nyckeln, så du kan inte återställa säkerhets kopian. Använd automatisk säkerhets kopiering och återställning av tidpunkter, eller Använd [kundhanterad (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) i stället. Du kan också inaktivera kryptering på databasen.
- Den största storleken för säkerhets kopierings skikt med kommandot `BACKUP` i en hanterad instans är 195 GB, vilket är den maximala BLOB-storleken. Öka antalet ränder i säkerhets kopierings kommandot för att minska storleken på enskilda stripe-volymer och håll dig inom den här gränsen.

    > [!TIP]
    > För att undvika den här begränsningen när du säkerhetskopierar en databas från antingen SQL Server i en lokal miljö eller på en virtuell dator kan du:
    >
    > - Säkerhetskopiera till `DISK` i stället för att säkerhetskopiera till `URL`.
    > - Överför säkerhetskopieringsfilerna till Blob Storage.
    > - Återställ till den hanterade instansen.
    >
    > Kommandot `Restore` i en hanterad instans har stöd för större blob-storlekar i säkerhetskopieringsfilerna eftersom en annan Blob-typ används för lagring av de uppladdade säkerhets kopiorna.

Information om säkerhets kopior med T-SQL finns i [säkerhets kopiering](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Säkerhet

### <a name="auditing"></a>Granskning

De viktigaste skillnaderna mellan granskning i databaser i Azure SQL Database och databaser i SQL Server är:

- Med alternativet för distribution av hanterad instans i Azure SQL Database fungerar granskning på server nivå. Loggfilerna för `.xel` lagras i Azure Blob Storage.
- Med distributions alternativen enkel databas och elastisk pool i Azure SQL Database, fungerar granskning på databas nivå.
- I SQL Server lokala eller virtuella datorer fungerar granskning på server nivå. Händelser lagras i fil systemet eller i Windows-händelseloggen.
 
XEvent-granskning i hanterade instanser stöder Azure Blob Storage-mål. Fil-och Windows-loggar stöds inte.

Viktiga skillnader i syntaxen i `CREATE AUDIT` för granskning till Azure Blob Storage är:

- En ny syntax `TO URL` anges att du kan använda för att ange URL: en för Azure Blob storage-behållaren där `.xel`-filerna placeras.
- Syntaxen `TO FILE` stöds inte eftersom en hanterad instans inte kan komma åt Windows-filresurser.

Mer information finns i: 

- [SKAPA SERVER GRANSKNING](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikat

En hanterad instans kan inte komma åt fil resurser och Windows-mappar, så följande villkor gäller:

- @No__t-0 @ no__t-1 @ no__t-2-filen stöds inte för certifikat.
- @No__t-0 @ no__t-1 @ no__t-2-certifikatet från `FILE` @ no__t-4 @ no__t-5 stöds inte. Det går inte att använda filer för privata nycklar. 

Se [Skapa certifikat](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) -och [säkerhets kopierings certifikat](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Lösning**: I stället för att skapa en säkerhets kopia av certifikatet och återställa säkerhets kopian [hämtar du det binära innehållet och den privata nyckeln, lagrar det som. SQL-fil och skapar från binär](https://docs.microsoft.com/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Autentiseringsuppgift

Endast Azure Key Vault-och `SHARED ACCESS SIGNATURE`-identiteter stöds. Windows-användare stöds inte.

Se [skapa autentiseringsuppgifter](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) och [ändra autentiseringsuppgifter](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kryptografiproviders

En hanterad instans kan inte komma åt filer, så det går inte att skapa kryptografiska providers:

- `CREATE CRYPTOGRAPHIC PROVIDER` stöds inte. Se [skapa kryptografiprovider](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` stöds inte. Se [Alter CRYPTOGRAPHIC Provider](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Inloggningar och användare

- SQL-inloggningar som skapats med hjälp av `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` och `FROM SID` stöds. Se [Skapa inloggning](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) Server huvud namn (inloggningar) som skapats med syntaxen [create login](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) eller [create User from login [Azure AD login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) stöds (offentlig för hands version). Dessa inloggningar skapas på server nivå.

    Den hanterade instansen stöder Azure AD Database-huvudobjekten med syntaxen `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Den här funktionen kallas även för Azure AD-inneslutna databas användare.

- Windows-inloggningar som skapats med syntaxen `CREATE LOGIN ... FROM WINDOWS` stöds inte. Använd Azure Active Directory inloggningar och användare.
- Den Azure AD-användare som skapade instansen har [obegränsade administratörs privilegier](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Användare som inte har administratörs behörighet på Azure AD kan skapas med hjälp av syntaxen `CREATE USER ... FROM EXTERNAL PROVIDER`. Se [CREATE-användare... FRÅN EXTERN PROVIDER @ NO__T-0.
- Azure AD server-Huvudkonton (inloggningar) stöder endast SQL-funktioner inom en hanterad instans. Funktioner som kräver interaktion mellan olika instanser, oavsett om de ligger inom samma Azure AD-klient eller olika klienter, stöds inte för Azure AD-användare. Exempel på sådana funktioner är:

  - SQL-transaktionell replikering.
  - Länka Server.

- Att ange en Azure AD-inloggning som är mappad till en Azure AD-grupp eftersom databas ägaren inte stöds.
- Personifiering av Azure AD-huvudobjekt på server nivå med hjälp av andra Azure AD-huvudobjekt stöds, till exempel [execute as](/sql/t-sql/statements/execute-as-transact-sql) -satsen. Kör som-begränsningar är:

  - Kör som-användare stöds inte för Azure AD-användare när namnet skiljer sig från inloggnings namnet. Ett exempel är när användaren skapas med syntaxen CREATE USER [myAadUser] FROM LOGIn [john@contoso.com] och personifiering görs via EXEC AS USER = _myAadUser_. När du skapar en **användare** från ett Azure AD server-huvudobjekt (inloggning) anger du användar namn som samma Login_name från **inloggningen**.
  - Endast SQL Server nivå huvud konton (inloggningar) som är en del av rollen `sysadmin` kan köra följande åtgärder som är riktade till Azure AD-huvud konton:

    - KÖRA SOM ANVÄNDARE
    - KÖRA SOM INLOGGNING

- Offentlig för hands versions begränsningar för Azure AD server-huvudobjekt (inloggningar):

  - Active Directory administratörs begränsningar för hanterad instans:

    - Azure AD-administratören som används för att konfigurera den hanterade instansen kan inte användas för att skapa ett Azure AD server-huvudobjekt (inloggning) i den hanterade instansen. Du måste skapa det första Azure AD server-huvudobjektet (inloggning) med hjälp av ett SQL Server konto som är en `sysadmin`-roll. Den tillfälliga begränsningen tas bort när Azure AD server-huvudobjekten (inloggningar) blir allmänt tillgängliga. Om du försöker använda ett Azure AD-administratörskonto för att skapa inloggningen visas följande fel: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - För närvarande måste den första Azure AD-inloggningen som skapas i huvud databasen skapas av standard SQL Servers kontot (inte Azure AD) som är en `sysadmin`-roll med hjälp av [Skapa inloggning](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) från extern provider. Den här begränsningen tas bort efter den allmänna tillgängligheten. Sedan kan du skapa en första Azure AD-inloggning med hjälp av Active Directory-administratören för en hanterad instans.
    - DacFx (export/import) som används med SQL Server Management Studio eller SqlPackage stöds inte för Azure AD-inloggningar. Den här begränsningen tas bort när Azure AD server-huvudobjekten (inloggningar) blir allmänt tillgängliga.
    - Använda Azure AD server-Huvudkonton (inloggningar) med SQL Server Management Studio:

      - Skript för Azure AD-inloggningar som använder alla autentiserade inloggningar stöds inte.
      - IntelliSense känner inte igen instruktionen Skapa inloggning från extern PROVIDER och visar en röd understrykning.

- Endast huvud inloggningen på server nivå, som skapas av den hanterade instans etablerings processen, medlemmar i Server rollerna, till exempel `securityadmin` eller `sysadmin` eller andra inloggningar med ändra alla INLOGGNINGs behörigheter på server nivå kan skapa Azure AD server-huvudobjekt (inloggningar) i huvud databasen för hanterad instans.
- Om inloggningen är ett SQL-huvud kan endast inloggningar som är en del av rollen `sysadmin` använda kommandot Skapa för att skapa inloggningar för ett Azure AD-konto.
- Azure AD-inloggningen måste vara medlem i en Azure AD i samma katalog som används för Azure SQL Database Hanterad instans.
- Azure AD server-Huvudkonton (inloggningar) visas i Object Explorer som börjar med SQL Server Management Studio 18,0 Preview 5.
- Överlappande Azure AD server-huvudobjekt (inloggningar) med ett administratörs konto för Azure AD tillåts. Azure AD server-Huvudkonton (inloggningar) prioriteras över Azure AD-administratören när du löser huvud kontot och tillämpar behörigheter på den hanterade instansen.
- Under autentiseringen används följande sekvens för att lösa det autentiserande huvudobjektet:

    1. Om Azure AD-kontot finns som direkt mappat till Azure AD server-huvudobjektet (inloggning) som finns i sys. server_principals som typen "E", bevilja åtkomst och tillämpa behörigheter för Azure AD server-huvudobjektet (inloggning).
    2. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till Azure AD server-huvudobjektet (inloggning) som finns i sys. server_principals som typen "X", bevilja åtkomst och tillämpa behörigheter för Azure AD-gruppinloggningen.
    3. Om Azure AD-kontot är en särskild Portal-konfigurerad Azure AD-administratör för en hanterad instans som inte finns i systemvyer för hanterade instanser, ska du använda särskilda fasta behörigheter för Azure AD-administratören för en hanterad instans (bakåtkompatibelt läge).
    4. Om Azure AD-kontot finns som direkt mappat till en Azure AD-användare i en databas, som finns i sys. database_principals som typen "E", bevilja åtkomst och tillämpa behörigheter för Azure AD Database-användaren.
    5. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till en Azure AD-användare i en databas, som finns i sys. database_principals som typen "X", bevilja åtkomst och tillämpa behörigheter för Azure AD-gruppinloggningen.
    6. Om det finns en Azure AD-inloggning som är mappad till antingen ett Azure AD-användarkonto eller ett Azure AD-gruppkonto, som matchar den användare som autentiseras, används alla behörigheter från den här Azure AD-inloggningen.

### <a name="service-key-and-service-master-key"></a>Tjänst nyckel och tjänstens huvud nyckel

- [Säkerhets kopiering av huvud nycklar](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) stöds inte (hanteras av SQL Database tjänsten).
- [Master Key Restore](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) stöds inte (hanteras av SQL Database tjänsten).
- [Säkerhets kopiering av tjänstens huvud nyckel](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) stöds inte (hanteras av SQL Database tjänsten).
- Det finns inte stöd för [återställning av tjänstens huvud nyckel](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) (hanteras av SQL Database tjänsten).

## <a name="configuration"></a>Konfiguration

### <a name="buffer-pool-extension"></a>Buffertpooltillägget

- [Buffertpooltillägget](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) stöds inte.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` stöds inte. Se [Alter Server Configuration](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortering

Standard instans sorteringen är `SQL_Latin1_General_CP1_CI_AS` och kan anges som en skapande parameter. Se [sorteringar](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Efterlevnadsnivåer

- De kompatibilitetsnivå som stöds är 100, 110, 120, 130, 140 och 150.
- Kompatibilitetsnivån under 100 stöds inte.
- Standard kompatibilitetsnivån för nya databaser är 140. För återställda databaser förblir kompatibilitetsnivån oförändrad om den var 100 och högre.

Se [ändra kompatibilitetsnivån för databas](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Databasspegling

Databas spegling stöds inte.

- `ALTER DATABASE SET PARTNER` och `SET WITNESS`-alternativ stöds inte.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` stöds inte.

Mer information finns i [ändra databas uppsättnings partner och ange vittne](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) och [skapa slut punkt... FÖR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Databas alternativ

- Flera loggfiler stöds inte.
- InMemory-objekt stöds inte i Generell användning tjänst nivå. 
- Det finns en gräns på 280 filer per Generell användning instans, vilket innebär högst 280 filer per databas. Både data-och loggfiler på Generell användning nivån räknas mot den här gränsen. [Affärskritisk nivån stöder 32 767-filer per databas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Databasen får inte innehålla fil grupper som innehåller FILESTREAM-data. Återställningen Miss lyckas om. bak innehåller `FILESTREAM`-data. 
- Varje fil placeras i Azure Blob Storage. I/o och data flöde per fil beror på storleken på varje enskild fil.

#### <a name="create-database-statement"></a>SKAPA databas uttryck

Följande begränsningar gäller för `CREATE DATABASE`:

- Det går inte att definiera filer och fil grupper. 
- Alternativet `CONTAINMENT` stöds inte. 
- `WITH`-alternativ stöds inte. 
   > [!TIP]
   > Som en lösning använder du `ALTER DATABASE` efter `CREATE DATABASE` för att ange databas alternativ för att lägga till filer eller för att ange inne slutning. 

- Alternativet `FOR ATTACH` stöds inte.
- Alternativet `AS SNAPSHOT OF` stöds inte.

Mer information finns i [skapa databas](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instruktionen ALTER DATABASE

Vissa fil egenskaper kan inte ställas in eller ändras:

- Det går inte att ange en fil Sök väg i `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL-instruktionen. Ta bort `FILENAME` från skriptet eftersom filerna placeras automatiskt på en hanterad instans. 
- Ett fil namn kan inte ändras med hjälp av `ALTER DATABASE`-instruktionen.

Följande alternativ är inställda som standard och kan inte ändras:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Följande alternativ kan inte ändras:

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

Mer information finns i [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Det finns för närvarande inte stöd för att aktivera och inaktivera SQL Server Agent i en hanterad instans. SQL Agent körs alltid.
- SQL Server Agent inställningarna är skrivskyddade. Proceduren `sp_set_agent_properties` stöds inte i en hanterad instans. 
- Jobb
  - Steg för T-SQL-jobb stöds.
  - Följande migreringsjobb stöds:
    - Transaktions logg läsare
    - Ögonblicksbild
    - Möjligheter
  - SSIS jobb steg stöds.
  - Andra typer av jobb-steg stöds inte för närvarande:
    - Jobb steget för Sammanslagningsreplikering stöds inte. 
    - Queue Reader stöds inte. 
    - Kommando gränssnittet stöds inte ännu.
  - Hanterade instanser kan inte komma åt externa resurser, till exempel nätverks resurser via Robocopy. 
  - SQL Server Analysis Services stöds inte.
- Meddelanden stöds delvis.
- E-postavisering stöds, även om det krävs att du konfigurerar en Database Mail-profil. SQL Server Agent kan bara använda en Database Mail profil och den måste anropas `AzureManagedInstance_dbmail_profile`. 
  - Pager stöds inte.
  - Netsend stöds inte.
  - Aviseringar stöds inte än.
  - Proxyservrar stöds inte.
- EventLog stöds inte.

Följande SQL Agent-funktioner stöds för närvarande inte:

- Proxy
- Schemalägga jobb på en inaktiv processor
- Aktivera eller inaktivera en agent
- Aviseringar

Information om SQL Server Agent finns i [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabeller

Följande tabell typer stöds inte:

- [-](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [Extern tabell](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) (PolyBase)
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (stöds inte endast i generell användning-nivån)

Information om hur du skapar och ändrar tabeller finns i [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) och [ändra tabell](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funktioner

### <a name="bulk-insert--openrowset"></a>Mass infogning/OpenRowSet

En hanterad instans kan inte komma åt fil resurser och Windows-mappar, så filerna måste importeras från Azure Blob Storage:

- `DATASOURCE` krävs i kommandot `BULK INSERT` när du importerar filer från Azure Blob Storage. Se [bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` krävs i `OPENROWSET`-funktionen när du läser innehållet i en fil från Azure Blob Storage. Se [OpenRowSet](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` kan användas för att läsa data från andra Azure SQL-databaser, hanterade instanser eller SQL Server instanser. Andra källor som Oracle-databaser eller Excel-filer stöds inte.

### <a name="clr"></a>CLR

En hanterad instans kan inte komma åt fil resurser och Windows-mappar, så följande villkor gäller:

- Endast `CREATE ASSEMBLY FROM BINARY` stöds. Se [skapa assem bly från Binary](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` stöds inte. Se [skapa sammansättning från fil](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` kan inte referera till filer. Se [Alter Assembly](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` kan inte skicka bilagor med hjälp av parametern @file_attachments. Lokalt fil system och externa resurser eller Azure Blob Storage kan inte nås från den här proceduren.
 - Se kända problem som rör parametern för `@query` och autentisering.
 
### <a name="dbcc"></a>DBCC

Inte dokumenterade DBCC-instruktioner som är aktiverade i SQL Server stöds inte i hanterade instanser.

- Endast ett begränsat antal globala spårnings flaggor stöds. @No__t på sessionsbaserade-0 stöds inte. Se [spårnings flaggor](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) och [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) fungerar med det begränsade antalet globala spårnings flaggor.
- Det går inte att använda [DBCC CHECKDB](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) med alternativen REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST och REPAIR_REBUILD eftersom databasen inte kan ställas in i `SINGLE_USER`-läge – se [ändra databas skillnader](#alter-database-statement). Potentiella databas fel hanteras av support teamet för Azure. Kontakta Azure-supporten om du är märker databas skada som bör åtgärdas.

### <a name="distributed-transactions"></a>Distribuerade transaktioner

MSDTC-och [elastiska transaktioner](sql-database-elastic-transactions-overview.md) stöds för närvarande inte i hanterade instanser.

### <a name="extended-events"></a>Extended Events

Vissa Windows-/regionsspecifika mål för utökade händelser (XEvents) stöds inte:

- @No__t-0-målet stöds inte. Lagra `.xel`-filer i Azure Blob Storage. Se [etw_classic_sync-mål](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- @No__t-0-målet stöds inte. Lagra `.xel`-filer i Azure Blob Storage. Se [event_file-mål](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externa bibliotek

I Database R och python stöds inte externa bibliotek ännu. Se [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FILESTREAM och FileTable

- FILESTREAM-data stöds inte.
- Databasen får inte innehålla fil grupper med `FILESTREAM`-data.
- `FILETABLE` stöds inte.
- Tabeller kan inte ha `FILESTREAM`-typer.
- Följande funktioner stöds inte:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Mer information finns i [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) och [FileTable](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Semantisk sökning i full text

[Semantisk sökning](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) stöds inte.

### <a name="linked-servers"></a>Länkade servrar

Länkade servrar i hanterade instanser har stöd för ett begränsat antal mål:

- Mål som stöds är hanterade instanser, enskilda databaser och SQL Server instanser. 
- Länkade servrar har inte stöd för distribuerade skrivbara transaktioner (MS DTC).
- Mål som inte stöds är filer, Analysis Services och andra RDBMS. Försök att använda intern CSV-import från Azure Blob Storage att använda `BULK INSERT` eller `OPENROWSET` som ett alternativ för fil import.

Åtgärder

- Skriv transaktioner över instanser stöds inte.
- `sp_dropserver` stöds för att släppa en länkad server. Se [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Funktionen `OPENROWSET` kan endast användas för att köra frågor på SQL Server instanser. De kan antingen hanteras, lokalt eller på virtuella datorer. Se [OpenRowSet](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- Funktionen `OPENDATASOURCE` kan endast användas för att köra frågor på SQL Server instanser. De kan antingen hanteras, lokalt eller på virtuella datorer. Endast `SQLNCLI`-, `SQLNCLI11`-och `SQLOLEDB`-värden stöds som en provider. Ett exempel är `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Se [OpenDataSource](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
- Det går inte att använda länkade servrar för att läsa filer (Excel, CSV) från nätverks resurserna. Försök att använda [bulk INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) eller [OpenRowSet](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) som läser CSV-filer från Azure Blob Storage. Spåra denna begäran på den [hanterade instansens feedback-objekt](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Externa tabeller som refererar till filerna i HDFS eller Azure Blob Storage stöds inte. Information om PolyBase finns i [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikering

- Ögonblicks bilder och dubbelriktade typer av replikering stöds. Sammanslagningsreplikering, peer-to-peer-replikering och uppdaterings bara prenumerationer stöds inte.
- [Transaktionell replikering](sql-database-managed-instance-transactional-replication.md) är tillgänglig för offentlig för hands version på en hanterad instans med vissa begränsningar:
    - Alla typer av replikeringspartner (utgivare, distributör, pull-prenumerant och push-prenumerant) kan placeras på hanterade instanser, men utgivaren och distributören måste antingen vara både i molnet eller både lokalt.
    - Hanterade instanser kan kommunicera med de senaste versionerna av SQL Server. Se de versioner som stöds [här](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
    - Transaktionsreplikering har vissa [ytterligare nätverks krav](sql-database-managed-instance-transactional-replication.md#requirements).

Information om hur du konfigurerar replikering finns i [själv studie kursen för replikering](replication-with-sql-database-managed-instance.md).


Om replikering har Aktiver ATS för en databas i en [failover-grupp](sql-database-auto-failover-group.md)måste den hanterade instans administratören rensa alla publikationer på den gamla primära och konfigurera om dem på den nya primära servern efter en redundansväxling. Följande aktiviteter behövs i det här scenariot:

1. Stoppa alla migreringsjobb som körs på databasen, om det finns några.
2. Släpp metadata för prenumerationer från utgivare genom att köra följande skript på utgivar databasen:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Ta bort metadata för prenumerationen från prenumeranten. Kör följande skript i prenumerations databasen på prenumerantens instans:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Tvinga bort alla replikeringsalternativ från utgivaren genom att köra följande skript i den publicerade databasen:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Framtvinga släppa gamla distributörer från den ursprungliga primära instansen (om det växlar tillbaka till en gammal primär som används för en distributör). Kör följande skript på huvud databasen i den tidigare hanterade distributörs instansen:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### <a name="restore-statement"></a>Instruktionen Restore 

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
  - `FROM URL` (Azure Blob Storage) är det enda alternativ som stöds.
  - `FROM DISK` @ no__t-1 @ no__t-2/enhet för säkerhets kopiering stöds inte.
  - Säkerhets kopierings uppsättningar stöds inte.
- `WITH`-alternativ stöds inte, till exempel ingen `DIFFERENTIAL` eller `STATS`.
- `ASYNC RESTORE`: Restore fortsätter även om klient anslutningen bryts. Om anslutningen bryts kan du kontrol lera status för en återställnings åtgärd i vyn `sys.dm_operation_status` och skapa och släppa databasen. Se [sys. DM _operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Följande databas alternativ anges eller åsidosätts och kan inte ändras senare: 

- `NEW_BROKER` om Service Broker inte är aktive rad i. bak-filen. 
- `ENABLE_BROKER` om Service Broker inte är aktive rad i. bak-filen. 
- `AUTO_CLOSE=OFF` om en databas i. bak-filen har `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` om en databas i. bak-filen har `SIMPLE` eller återställnings läget för @no__t 2.
- En minnesoptimerade fil grupp läggs till och anropas XTP om den inte finns i filen Source. bak. 
- Alla befintliga minnesoptimerade fil grupper byter namn till XTP. 
- `SINGLE_USER` och `RESTRICTED_USER`-alternativ konverteras till `MULTI_USER`.

Begränsningar: 

- Säkerhets kopior av skadade databaser kan återställas beroende på typen av skada, men automatiska säkerhets kopieringar görs inte förrän skadan har åtgärd ATS. Kontrol lera att du kör `DBCC CHECKDB` på käll instansen och Använd säkerhets kopierings `WITH CHECKSUM` för att förhindra det här problemet.
- Återställning av `.BAK`-fil av en databas som innehåller en begränsning som beskrivs i det här dokumentet (till exempel `FILESTREAM` eller `FILETABLE` objekt) kan inte återställas på den hanterade instansen.
- Det går inte att återställa `.BAK`-filer som innehåller flera säkerhets kopierings uppsättningar. 
- Det går inte att återställa `.BAK`-filer som innehåller flera loggfiler.
- Säkerhets kopior som innehåller databaser som är större än 8 TB, aktiva InMemory OLTP-objekt eller antal filer som skulle överskrida 280 filer per instans kan inte återställas på en Generell användning instans. 
- Säkerhets kopior som innehåller databaser som är större än 4 TB eller InMemory OLTP-objekt med Total storlek som är större än den storlek som beskrivs i [resurs gränser](sql-database-managed-instance-resource-limits.md) kan inte återställas affärskritisk-instansen.
Information om Restore-instruktioner finns i [restore Statements](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Samma begränsningar gäller för inbyggd återställnings åtgärd för tidpunkter. Generell användning databas som är större än 4 TB kan till exempel inte återställas på Affärskritisk instansen. Affärskritisk databas med InMemory OLTP-filer eller mer än 280 filer kan inte återställas på Generell användning instansen.

### <a name="service-broker"></a>Service Broker

Service Broker för överinstans stöds inte:

- `sys.routes`: Som en förutsättning måste du välja adressen från sys. routes. Adressen måste vara lokal på varje väg. Se [sys. routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Du kan inte använda `CREATE ROUTE` med `ADDRESS` förutom `LOCAL`. Se [skapa väg](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Du kan inte använda `ALTER ROUTE` med `ADDRESS` förutom `LOCAL`. Se [Alter Route](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Lagrade procedurer, funktioner och utlösare

- `NATIVE_COMPILATION` stöds inte i Generell användnings nivån.
- Följande [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) -alternativ stöds inte: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` stöds inte. Se [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` stöds inte. Se [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` stöds inte, vilket innefattar `sp_addextendedproc` @ no__t-2and `sp_dropextendedproc`. Se [utökade lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` och `sp_detach_db` stöds inte. Se [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)och [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>System funktioner och variabler

Följande variabler, funktioner och vyer returnerar olika resultat:

- `SERVERPROPERTY('EngineEdition')` returnerar värdet 8. Den här egenskapen identifierar en hanterad instans unikt. Se [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` returnerar NULL eftersom begreppet instans som det finns för SQL Server inte gäller för en hanterad instans. Se [SERVERPROPERTY (' instancename ')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` returnerar ett fullständigt DNS "anslutnings bara" namn, till exempel my-managed-instance.wcus17662feb9ce98.database.windows.net. Se [@ @SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` returnerar ett fullständigt DNS "anslutnings bara" namn, till exempel `myinstance.domain.database.windows.net` för egenskaperna "name" och "data_source". Se [sys. SERVRAR](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` returnerar NULL eftersom begreppet tjänst som det finns för SQL Server inte gäller för en hanterad instans. Se [@ @SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` stöds. Den returnerar NULL om Azure AD-inloggningen inte finns i sys. syslogins. Se [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` stöds inte. Felaktiga data returneras, vilket är ett tillfälligt känt problem. Se [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Miljö begränsningar

### <a name="subnet"></a>Subnet
-  Du kan inte placera andra resurser (till exempel virtuella datorer) i under nätet där du har distribuerat din hanterade instans. Distribuera de här resurserna med ett annat undernät.
- Under nätet måste ha tillräckligt många tillgängliga [IP-adresser](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Minimivärdet är 16 och rekommendationen måste ha minst 32 IP-adresser i under nätet.
- [Tjänstens slut punkter kan inte kopplas till under nätet för den hanterade instansen](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Kontrol lera att alternativet tjänst slut punkter är inaktiverat när du skapar det virtuella nätverket.
- Antalet virtuella kärnor och typer av instanser som du kan distribuera i en region har vissa [begränsningar och begränsningar](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Det finns vissa [säkerhets regler som måste tillämpas på under nätet](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>Virtuellt nätverk
- VNet kan distribueras med hjälp av resurs modellen – den klassiska modellen för VNet stöds inte.
- När en hanterad instans har skapats går det inte att flytta den hanterade instansen eller det virtuella nätverket till en annan resurs grupp eller prenumeration.
- Vissa tjänster, till exempel App Service miljöer, Logic Apps och hanterade instanser (som används för geo-replikering, Transaktionsreplikering eller via länkade servrar) kan inte komma åt hanterade instanser i olika regioner om deras virtuella nätverk är anslutna med [Global peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Du kan ansluta till dessa resurser via ExpressRoute eller VNet-till-VNet via VNet-gatewayer.

### <a name="tempdb"></a>TEMPDB

Den maximala fil storleken på `tempdb` får inte vara större än 24 GB per kärna på en Generell användning nivå. Den maximala storleken på @no__t 0 på en Affärskritisk nivå begränsas av instans lagrings storleken. `Tempdb`-logg filens storlek är begränsad till 120 GB både på Generell användning och Affärskritisk nivåer. Vissa frågor kan returnera ett fel om de behöver mer än 24 GB per kärna i `tempdb` eller om de producerar mer än 120 GB loggdata.

### <a name="error-logs"></a>Felloggar

En hanterad instans placerar utförlig information i fel loggarna. Det finns många interna system händelser som loggas i fel loggen. Använd en anpassad procedur för att läsa fel loggar som filtrerar bort vissa irrelevanta poster. Mer information finns i [Managed instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

## <a name="Issues"></a>Kända problem

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Ett fel fel returnerades vid försök att ta bort en fil som inte är tom

**Ikraftträdande** Okt 2019

SQL Server/hanterad instans [tillåter inte att användaren släpper en fil som inte är tom](https://docs.microsoft.com/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Om du försöker ta bort en data fil som inte är tom med `ALTER DATABASE REMOVE FILE`-instruktionen returneras inte felet `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` omedelbart. Den hanterade instansen fortsätter att försöka släppa filen och åtgärden kommer att Miss sen efter 30 min med `Internal server error`.

**Lösning**: Ta bort innehållet i filen med kommandot `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)`. Om det här är den enda filen i fil gruppen måste du ta bort data från tabellen eller partitionen som är kopplade till den här fil gruppen innan du krymper filen och eventuellt läsa in dessa data till en annan tabell/partition.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Ändra tjänst nivå och skapa instans åtgärder blockeras av pågående databas återställning

**Ikraftträdande** Sep 2019

Kontinuerliga `RESTORE`-instruktioner, migreringsprocessen för datamigrering och inbyggd tids återställning kommer att blockera uppdatering av tjänst nivån eller ändra storlek på den befintliga instansen och skapa nya instanser tills återställnings processen har slutförts. Med återställnings processen blockeras dessa åtgärder på hanterade instanser och instans-pooler i samma undernät där återställnings processen körs. Instanserna i instans pooler påverkas inte. Det går inte att skapa eller ändra åtgärder på tjänst nivå eller tids gräns – de fortsätter när återställnings processen har slutförts eller avbrutits.

**Lösning**: Vänta tills återställnings processen har slutförts, eller Avbryt återställnings processen om åtgärden för att skapa eller uppdatera tjänst nivå har högre prioritet.

### <a name="missing-validations-in-restore-process"></a>Valideringar som saknas i återställnings processen

**Ikraftträdande** Sep 2019

`RESTORE`-instruktionen och inbyggd återställnings punkt utför inte några nessecary-kontroller på den återställda databasen:
- **DBCC CHECKDB** -  @ no__t-2-instruktionen utför inte `DBCC CHECKDB` på den återställda databasen. Om en ursprunglig databas är skadad eller om en säkerhets kopia skadas när den kopieras till Azure Blob Storage, kommer automatiska säkerhets kopieringar inte att vidtas och Azure-supporten kommer att kontakta kunden. 
- Inbyggd återställnings process för tidpunkter kontrollerar inte att den automatiska säkerhets kopieringen från Affärskritisk instans innehåller [InMemory OLTP-objekt](sql-database-in-memory.md#in-memory-oltp). 

**Lösning**: Se till att du kör `DBCC CHECKDB` på käll databasen innan du tar en säkerhets kopia och använder alternativet `WITH CHECKSUM` i säkerhets kopiering för att undvika potentiella fel som kan återställas på den hanterade instansen. Kontrol lera att käll databasen inte innehåller [InMemory OLTP-objekt](sql-database-in-memory.md#in-memory-oltp) om du återställer den på generell användning nivå.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor på Affärskritisk tjänst nivå kan behöva konfigureras om efter en redundansväxling

**Ikraftträdande** Sep 2019

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) funktion som gör att du kan begränsa de resurser som är tilldelade till användarens arbets belastning kan klassificera vissa användares arbets belastning efter redundansväxling eller en användarinitierad ändring av tjänst nivån (till exempel ändringen av Max vCore eller Max instans lagrings storlek).

**Lösning**: Kör `ALTER RESOURCE GOVERNOR RECONFIGURE` regelbundet eller som en del av SQL Agent-jobbet som kör SQL-aktiviteten när instansen startar om du använder [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor).

### <a name="cannot-authenticate-to-external-mail-servers-using-secure-connection-ssl"></a>Det går inte att autentisera till externa e-postservrar med säker anslutning (SSL)

**Ikraftträdande** Aug 2019

Database mail som har [kon figurer ATS med säker anslutning (SSL)](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-database-mail) kan inte autentiseras för vissa e-postservrar utanför Azure. Detta är ett säkerhets konfigurations problem som kommer att lösas snart.

**Korrigera** Tillfälligt ta bort säker anslutning (SSL) från Database mail-konfigurationen tills problemet har lösts. 

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Service Broker dialog rutor mellan databaser måste initieras igen efter uppgraderingen av service nivå

**Ikraftträdande** Aug 2019

Service Broker dialog rutor mellan databaser slutar att leverera meddelanden till tjänsterna i andra databaser efter åtgärden ändra tjänst nivå. Meddelandena går **inte förlorade** och de finns i avsändar kön. Om du ändrar virtuella kärnor eller instans lagrings storlek i hanterade instanser kommer `service_broke_guid`-värdet i [sys. Databass](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) -vyn att ändras för alla databaser. Alla `DIALOG` som skapats med DIALOG instruktionen [BEGIN](https://docs.microsoft.com/en-us/sql/t-sql/statements/begin-dialog-conversation-transact-sql) som refererar till tjänst utjämnare i andra databaser kommer att sluta leverera meddelanden till mål tjänsten.

**Korrigera** Stoppa alla aktiviteter som använder Service Broker dialog samtal över flera databaser innan du uppdaterar tjänst nivån och återinitierar dem igen. Om det finns återstående meddelanden som inte levereras efter ändringar i tjänst nivån läser du meddelandena från käll kön och skickar dem igen till målkön.

### <a name="impersonification-of-aad-login-types-is-not-supported"></a>Impersonification av inloggnings typer för AAD stöds inte

**Ikraftträdande** Juli 2019

Personifiering med `EXECUTE AS USER` eller `EXECUTE AS LOGIN` av följande AAD-huvudobjekt stöds inte:
-   AAD-användare med alias. Följande fel returneras i det här fallet `15517`.
- AAD-inloggningar och användare baserat på AAD-program eller tjänstens huvud namn. Följande fel returneras i det här fallet `15517` och `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query-parametern stöds inte i sp_send_db_mail

**Ikraftträdande** April 2019

Parametern `@query` i [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) -proceduren fungerar inte.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Transaktionsreplikering måste konfigureras om efter GEO-redundans

**Ikraftträdande** Mar 2019

Om transaktionell replikering har Aktiver ATS för en databas i en grupp för automatisk redundans, måste den hanterade instans administratören rensa alla publikationer på den gamla primära servern och konfigurera om dem på den nya primära servern efter en redundansväxling till en annan region. Se [replikering](#replication) för mer information.

### <a name="aad-logins-and-users-are-not-supported-in-tools"></a>AAD-inloggningar och användare stöds inte i verktyg

**Ikraftträdande** Jan 2019

SQL Server Management Studio och SQL Server Data Tools har inte fullt stöd för inloggningar och användare i Azure Active Directory.
- Att använda Azure AD server-Huvudkonton (inloggningar) och användare (offentlig för hands version) med SQL Server Data Tools stöds inte för närvarande.
- Skript för Azure AD server-Huvudkonton (inloggningar) och användare (offentlig för hands version) stöds inte i SQL Server Management Studio.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tillfällig databas används under återställnings åtgärden

När en databas återställs på den hanterade instansen skapar återställnings tjänsten först en tom databas med det önskade namnet för att allokera namnet på instansen. Efter en stund kommer den här databasen att tas bort och återställning av den faktiska databasen kommer att startas. Databasen som är i *återställnings* tillstånd kommer temporärt att ha ett slumpmässigt GUID-värde i stället för namn. Det tillfälliga namnet kommer att ändras till det önskade namnet som anges i `RESTORE`-instruktionen när återställnings processen har slutförts. I den inledande fasen kan användaren komma åt den tomma databasen och till och med skapa tabeller eller läsa in data i den här databasen. Den här tillfälliga databasen kommer att tas bort när återställnings tjänsten startar den andra fasen.

**Lösning**: Få inte åtkomst till den databas som du återställer förrän du ser att återställningen har slutförts.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB-strukturen och innehållet har skapats på nytt

@No__t-0-databasen delas alltid upp i 12 datafiler och fil strukturen kan inte ändras. Den maximala storleken per fil kan inte ändras och nya filer kan inte läggas till i `tempdb`. `Tempdb` återskapas alltid som en tom databas när instansen startar eller växlar över och eventuella ändringar som görs i `tempdb` kommer inte att bevaras.

### <a name="exceeding-storage-space-with-small-database-files"></a>Överskrida lagrings utrymme med små databasfiler

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`-och `RESTORE DATABASE`-instruktioner kan Miss Miss kan uppstå på grund av att instansen kan komma åt Azure Storage gränsen.

Varje Generell användning hanterad instans har upp till 35 TB lagring reserverat för Azure Premium-disk utrymme. Varje databas fil placeras på en separat fysisk disk. Disk storlekar kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Oanvänt utrymme på disken debiteras inte, men den totala summan av storleken på Azure Premium-diskar får inte överstiga 35 TB. I vissa fall kan en hanterad instans som inte behöver 8 TB totalt överskrida gränsen på 35 TB Azure på lagrings storleken på grund av intern fragmentering.

En Generell användning hanterad instans kan till exempel ha en stor fil som är 1,2 TB i storleken på en 4 TB-disk. Det kan också finnas 248 filer med en storlek på 1 GB som placeras på separata 128 GB-diskar. I det här exemplet:

- Den totala allokerade disk lagrings storleken är 1 x 4 TB + 248 x 128 GB = 35 TB.
- Det totala reserverade utrymmet för databaser på instansen är 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Det här exemplet illustrerar att under vissa omständigheter, på grund av en specifik distribution av filer, kan en hanterad instans uppnå gränsen på 35 TB som är reserverad för en ansluten Azure Premium-disk om du inte kan vänta på det.

I det här exemplet fortsätter befintliga databaser att fungera och kan växa utan problem så länge nya filer inte läggs till. Det går inte att skapa eller återställa nya databaser eftersom det inte finns tillräckligt med utrymme för nya disk enheter, även om den totala storleken på alla databaser inte når gränsen för instans storlek. Det fel som returneras i detta fall är inte klart.

Du kan [identifiera antalet återstående filer](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) med hjälp av systemvyer. Om du når den här gränsen kan du försöka att [tömma och ta bort några av de mindre filerna med hjälp av DBCC SHRINKFILE-instruktionen](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) eller växla till [Affärskritisks nivån, som inte har den här gränsen](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>GUID-värden som visas i stället för databas namn

Flera systemvyer, prestanda räknare, fel meddelanden, XEvents och fel logg poster visar GUID-databas identifierare i stället för de faktiska databas namnen. Använd inte dessa GUID-identifierare eftersom de ersätts med faktiska databas namn i framtiden.

### <a name="error-logs-arent-persisted"></a>Fel loggarna är inte beständiga

Fel loggar som är tillgängliga i den hanterade instansen är inte bestående och deras storlek ingår inte i den maximala lagrings gränsen. Fel loggar kan raderas automatiskt om redundansväxlingen sker. Det kan finnas luckor i fel logg historiken eftersom den hanterade instansen har flyttats flera gånger på flera virtuella datorer.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transaktions omfånget på två databaser inom samma instans stöds inte

Klassen `TransactionScope` i .NET fungerar inte om två frågor skickas till två databaser inom samma instans under samma transaktions omfång:

```csharp
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

Även om den här koden fungerar med data inom samma instans, krävs MSDTC.

**Korrigera** Använd [SQLConnection. ChangeDatabase (sträng)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) om du vill använda en annan databas i en anslutnings kontext i stället för att använda två anslutningar.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress

CLR-moduler placerade i en hanterad instans och länkade servrar eller distribuerade frågor som refererar till en aktuell instans kan ibland inte matcha IP-adressen för en lokal instans. Det här felet är ett tillfälligt problem.

**Korrigera** Använd kontext anslutningar i en CLR-modul om möjligt.

## <a name="next-steps"></a>Nästa steg

- Mer information om hanterade instanser finns i [Vad är en hanterad instans?](sql-database-managed-instance.md)
- En funktion och en jämförelse lista finns i [Azure SQL Database jämförelse](sql-database-features.md)av funktioner.
- En snabb start som visar hur du skapar en ny hanterad instans finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
