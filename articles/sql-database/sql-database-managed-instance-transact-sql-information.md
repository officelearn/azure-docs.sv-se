---
title: T-SQL-skillnader med hanterad instans
description: I den här artikeln beskrivs skillnaderna mellan T-SQL mellan en hanterad instans i Azure SQL Database och SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365487"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Hanterade T-SQL-skillnader och begränsningar för instanser

Den här artikeln sammanfattar och förklarar skillnaderna i syntax och beteende mellan Azure SQL Database hanterad instans och lokal SQL Server Database Engine. Distributionsalternativet för hanterade instanser ger hög kompatibilitet med lokal SQL Server-databasmotor. De flesta av SQL Server-databasmotorfunktionerna stöds i en hanterad instans.

![Migrering](./media/sql-database-managed-instance/migration.png)

Det finns vissa PaaS-begränsningar som introduceras i Hanterad instans och vissa beteendeändringar jämfört med SQL Server. Skillnaderna är indelade i följande kategorier:<a name="Differences"></a>

- [Tillgängligheten](#availability) inkluderar skillnaderna i [Alltid på tillgänglighetsgrupper](#always-on-availability-groups) och [säkerhetskopior](#backup).
- [Säkerhet](#security) omfattar skillnader i [granskning,](#auditing) [certifikat,](#certificates) [autentiseringsuppgifter,](#credential) [kryptografiska leverantörer,](#cryptographic-providers) [inloggningar och användare](#logins-and-users)samt [nyckeln till tjänstnyckel och tjänsthanterare](#service-key-and-service-master-key).
- [Konfigurationen](#configuration) omfattar skillnaderna i [buffertpooltillägg,](#buffer-pool-extension) [sortering,](#collation) [kompatibilitetsnivåer,](#compatibility-levels) [databasspegling,](#database-mirroring) [databasalternativ,](#database-options) [SQL Server Agent](#sql-server-agent)och [tabellalternativ](#tables).
- [Funktioner](#functionalities) inkluderar [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distribuerade transaktioner](#distributed-transactions), utökade [händelser](#extended-events), [externa bibliotek,](#external-libraries) [filestream och FileTable](#filestream-and-filetable), [semantisk sökning](#full-text-semantic-search)i fulltext , länkade [servrar](#linked-servers), [PolyBase](#polybase), [Replikering](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), [lagrade procedurer, funktioner och utlösare](#stored-procedures-functions-and-triggers).
- [Miljöinställningar](#Environment) som virtuella nätverk och nätkonfigurationer.

De flesta av dessa funktioner är arkitektoniska begränsningar och representerar servicefunktioner.

Tillfälliga kända problem som upptäcks i hanterade instanser och som kommer att lösas i framtiden beskrivs på [sidan för viktig information](sql-database-release-notes.md).

## <a name="availability"></a>Tillgänglighet

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Alltid på tillgänglighetsgrupper

[Hög tillgänglighet](sql-database-high-availability.md) är inbyggd i hanterad instans och kan inte styras av användare. Följande satser stöds inte:

- [SKAPA SLUTPUNKT ... FÖR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [SKAPA TILLGÄNGLIGHETSGRUPP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [GRUPPEN ÄNDRA TILLGÄNGLIGHET](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [GRUPPEN DROP-TILLGÄNGLIGHET](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR-satsen](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) i [ALTER DATABASE-satsen](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Säkerhetskopiering

Hanterade instanser har automatiska säkerhetskopior, `COPY_ONLY` så att användarna kan skapa fullständiga säkerhetskopieringar av databaser. Säkerhetskopiering av differentiella, logg- och filögonblicksbilder stöds inte.

- Med en hanterad instans kan du säkerhetskopiera en instansdatabas endast till ett Azure Blob-lagringskonto:
  - Endast `BACKUP TO URL` stöds.
  - `FILE`, `TAPE`och säkerhetskopieringsenheter stöds inte.
- De flesta `WITH` av de allmänna alternativen stöds.
  - `COPY_ONLY`är obligatoriskt.
  - `FILE_SNAPSHOT`stöds inte.
  - Bandalternativ: `REWIND` `NOREWIND`, `UNLOAD`, `NOUNLOAD` och stöds inte.
  - Loggspecifika alternativ: `NORECOVERY` `STANDBY`, `NO_TRUNCATE` och stöds inte.

Begränsningar: 

- Med en hanterad instans kan du säkerhetskopiera en instansdatabas till en säkerhetskopia med upp till 32 ränder, vilket är tillräckligt för databaser upp till 4 TB om säkerhetskopieringskomprimering används.
- Du kan inte `BACKUP DATABASE ... WITH COPY_ONLY` köra på en databas som är krypterad med tjänsthanterad transparent datakryptering (TDE). Tjänsthanterad TDE tvingar säkerhetskopior att krypteras med en intern TDE-nyckel. Nyckeln kan inte exporteras, så du kan inte återställa säkerhetskopian. Använd automatiska säkerhetskopior och point-in-time-återställning, eller använd [kundhanterad (BYOK) TDE](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) istället. Du kan också inaktivera kryptering i databasen.
- Den maximala stripe-storleken `BACKUP` för säkerhetskopiering med kommandot i en hanterad instans är 195 GB, vilket är den maximala blob-storleken. Öka antalet ränder i säkerhetskopieringskommandot för att minska enskilda stripe-storlekar och hålla dig inom den här gränsen.

    > [!TIP]
    > Om du vill kringgå den här begränsningen kan du när du säkerhetskopierar en databas från antingen SQL Server i en lokal miljö eller på en virtuell dator:
    >
    > - Säkerhetskopiera `DISK` till istället för `URL`att säkerhetskopiera till .
    > - Ladda upp säkerhetskopiorna till Blob-lagring.
    > - Återställ till den hanterade instansen.
    >
    > Kommandot `Restore` i en hanterad instans stöder större blob-storlekar i säkerhetskopieringsfilerna eftersom en annan blob-typ används för lagring av de uppladdade säkerhetskopiorna.

Information om säkerhetskopior med T-SQL finns i [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Säkerhet

### <a name="auditing"></a>Granskning

De viktigaste skillnaderna mellan granskning i databaser i Azure SQL Database och databaser i SQL Server är:

- Med distributionsalternativet för hanterade instanser i Azure SQL Database fungerar granskning på servernivå. Loggfilerna `.xel` lagras i Azure Blob-lagring.
- Med de enskilda alternativen för distribution av en databas och elastisk pooldistribution i Azure SQL Database fungerar granskning på databasnivå.
- I lokala eller virtuella SQL Server-datorer fungerar granskning på servernivå. Händelser lagras i filsystem eller Windows-händelseloggar.
 
XEvent-granskning i hanterad instans stöder Azure Blob-lagringsmål. Fil- och Windows-loggar stöds inte.

De viktigaste skillnaderna `CREATE AUDIT` i syntaxen för granskning till Azure Blob-lagring är:

- En ny `TO URL` syntax tillhandahålls som du kan använda för att ange `.xel` URL:en för Azure Blob-lagringsbehållaren där filerna placeras.
- Syntaxen `TO FILE` stöds inte eftersom en hanterad instans inte kan komma åt Windows-filresurser.

Mer information finns i: 

- [SKAPA SERVERGRANSKNING](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ÄNDRA SERVERGRANSKNING](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Granskning](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certifikat

En hanterad instans kan inte komma åt filresurser och Windows-mappar, så följande begränsningar gäller:

- `CREATE FROM` / Filen `BACKUP TO` stöds inte för certifikat.
- Certifikatet `CREATE` / `BACKUP` `FILE` / från `ASSEMBLY` stöds inte. Privata nyckelfiler kan inte användas. 

Se [SKAPA CERTIFIKAT-](/sql/t-sql/statements/create-certificate-transact-sql) och [SÄKERHETSKOPIERINGSCERTIFIKAT](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Lösning:** I stället för att skapa säkerhetskopiering av certifikat och återställa [säkerhetskopian, hämta certifikatet binärt innehåll och privat nyckel, lagra det som .sql-fil och skapa från binär:](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Autentiseringsuppgift

Endast Azure Key `SHARED ACCESS SIGNATURE` Vault och identiteter stöds. Windows-användare stöds inte.

Se [SKAPA AUTENTISERINGSUPPGIFTER](/sql/t-sql/statements/create-credential-transact-sql) och [ÄNDRA AUTENTISERINGSUPPGIFTER](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Kryptografiska leverantörer

En hanterad instans kan inte komma åt filer, så kryptografiska providers kan inte skapas:

- `CREATE CRYPTOGRAPHIC PROVIDER`stöds inte. Se [SKAPA KRYPTOGRAFISK LEVERANTÖR](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`stöds inte. Se [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Inloggningar och användare

- SQL-inloggningar som `FROM CERTIFICATE` `FROM ASYMMETRIC KEY`skapats `FROM SID` med hjälp av , och stöds. Se [SKAPA INLOGGNING](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) serverhuvudnamn (inloggningar) som skapats med semtaxen [SKAPA INLOGGNING](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) eller i syntaxen [SKAPA ANVÄNDARE FRÅN INLOGGNING [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) stöds. Dessa inloggningar skapas på servernivå.

    Hanterad instans stöder Azure AD-databashuvudnamn med syntaxen `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Den här funktionen kallas även Azure AD-innehållna databasanvändare.

- Windows-inloggningar som `CREATE LOGIN ... FROM WINDOWS` skapats med syntaxen stöds inte. Använd Azure Active Directory-inloggningar och användare.
- Azure AD-användare som skapade instansen har [obegränsad administratörsbehörighet](sql-database-manage-logins.md).
- Användare på Azure AD-databasnivå som inte `CREATE USER ... FROM EXTERNAL PROVIDER` är administratörer kan skapas med hjälp av syntaxen. Se [SKAPA ANVÄNDARE ... FRÅN EXTERN LEVERANTÖR](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Azure AD-serverhuvudnamn (inloggningar) stöder SQL-funktioner endast i en hanterad instans. Funktioner som kräver interaktion mellan instanser, oavsett om de finns inom samma Azure AD-klientorganisation eller olika klienter, stöds inte för Azure AD-användare. Exempel på sådana funktioner är:

  - SQL transaktionsreplikering.
  - Länkserver.

- Ange en Azure AD-inloggning som mappas till en Azure AD-grupp eftersom databasägaren inte stöds.
- Personifiering av Azure AD-server-princip med hjälp av andra Azure AD-huvudnamn stöds, till exempel EXECUTE AS-satsen. [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) KÖR AS begränsningar är:

  - KÖR SOM ANVÄNDARE stöds inte för Azure AD-användare när namnet skiljer sig från inloggningsnamnet. Ett exempel är när användaren skapas via syntaxen SKAPA ANVÄNDARE [myAadUser] FRÅN LOGIN [john@contoso.com] och personifiering görs via EXEC SOM ANVÄNDARE = _myAadUser_. När du skapar en **ANVÄNDARE** från ett huvudnamn för Azure AD-servern (inloggning) anger du user_name som samma login_name från **LOGIN**.
  - Endast huvudnamnen på SQL Server-nivå (inloggningar) som ingår i `sysadmin` rollen kan utföra följande åtgärder som är inriktade på Azure AD-huvudnamn:

    - KÖRA SOM ANVÄNDARE
    - KÖRA SOM INLOGGNING

- Databasexport/import med bacpac-filer stöds för Azure AD-användare i hanterad instans med antingen [SSMS V18.4 eller senare](/sql/ssms/download-sql-server-management-studio-ssms)eller [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - Följande konfigurationer stöds med hjälp av databas bacpac-filen: 
    - Exportera/importera en databas mellan olika hanteringsinstanser inom samma Azure AD-domän.
    - Exportera en databas från hanterad instans och importera till SQL Database inom samma Azure AD-domän. 
    - Exportera en databas från SQL Database och importera till hanterad instans inom samma Azure AD-domän.
    - Exportera en databas från hanterad instans och importera till SQL Server (version 2012 eller senare).
      - I den här konfigurationen skapas alla Azure AD-användare som SQL-databasobjekt (användare) utan inloggningar. Typen av användare visas som SQL (visas som SQL_USER i sys.database_principals). Deras behörigheter och roller finns kvar i SQL Server-databasens metadata och kan användas för personifiering. De kan dock inte användas för att komma åt och logga in på SQL Server med hjälp av sina autentiseringsuppgifter.

- Endast huvudinloggningen på servernivå, som skapas av etableringsprocessen för hanterade `securityadmin` instanser, medlemmar i serverrollerna, till exempel eller `sysadmin`, eller andra inloggningar med ÄNDRA EVENTUELL INLOGGNINGSBEHÖRIGHET PÅ servernivå, kan skapa Azure AD-serverobjekt (inloggningar) i huvuddatabasen för hanterad instans.
- Om inloggningen är ett SQL-huvudnamn kan `sysadmin` endast inloggningar som ingår i rollen använda kommandot Skapa för att skapa inloggningar för ett Azure AD-konto.
- Azure AD-inloggningen måste vara medlem i en Azure AD i samma katalog som används för Azure SQL Database-hanterad instans.
- Azure AD-serverhuvudnamn (inloggningar) visas i Object Explorer som börjar med SQL Server Management Studio 18.0 preview 5.
- Överlappande Azure AD-serverhuvudnamn (inloggningar) med ett Azure AD-administratörskonto är tillåtet. Azure AD-serverhuvudnamn (inloggningar) har företräde framför Azure AD-administratören när du matchar huvudmannen och tillämpar behörigheter för den hanterade instansen.
- Under autentiseringen används följande sekvens för att lösa det autentiserade huvudmannen:

    1. Om Azure AD-kontot finns som direkt mappas till Azure AD-serverns huvudnamn (inloggning), som finns i sys.server_principals som typ "E", beviljar du åtkomst och tillämpar behörigheter för Azure AD-serverns huvudnamn (inloggning).
    2. Om Azure AD-kontot är medlem i en Azure AD-grupp som är mappad till Azure AD-serverns huvudnamn (inloggning), som finns i sys.server_principals som typ "X", beviljar du åtkomst och tillämpar behörigheter för Azure AD-gruppinloggningen.
    3. Om Azure AD-kontot är en särskild portalkonfigurerad Azure AD-administratör för hanterad instans, som inte finns i systemvyer för hanterade instanser, tillämpar du särskilda fasta behörigheter för Azure AD-administratören för hanterad instans (äldre läge).
    4. Om Azure AD-kontot finns som direkt mappas till en Azure AD-användare i en databas, som finns i sys.database_principals som typ "E", beviljar och tillämpar behörigheter för Azure AD-databasanvändaren.
    5. Om Azure AD-kontot är medlem i en Azure AD-grupp som mappas till en Azure AD-användare i en databas, som finns i sys.database_principals som typ "X", beviljar du åtkomst och tillämpar behörigheter för Azure AD-gruppinloggningen.
    6. Om det finns en Azure AD-inloggning mappad till antingen ett Azure AD-användarkonto eller ett Azure AD-gruppkonto, som matchar till användaren som autentiserar, tillämpas alla behörigheter från den här Azure AD-inloggningen.

### <a name="service-key-and-service-master-key"></a>Nyckel till service och servicehanterare

- [Säkerhetskopiering](/sql/t-sql/statements/backup-master-key-transact-sql) av huvudnyckel stöds inte (hanteras av SQL Database-tjänsten).
- Återställning av [huvudnyckel](/sql/t-sql/statements/restore-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten).
- [Säkerhetskopiering](/sql/t-sql/statements/backup-service-master-key-transact-sql) av tjänstnyckel stöds inte (hanteras av SQL Database-tjänsten).
- [Återställning av tjänsthuvudnyckel](/sql/t-sql/statements/restore-service-master-key-transact-sql) stöds inte (hanteras av SQL Database-tjänsten).

## <a name="configuration"></a>Konfiguration

### <a name="buffer-pool-extension"></a>Tillägg för buffertpool

- [Buffertpooltillägg](/sql/database-engine/configure-windows/buffer-pool-extension) stöds inte.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`stöds inte. Se [ÄNDRA SERVERKONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortering

Standardinstansinsamlingen `SQL_Latin1_General_CP1_CI_AS` är och kan anges som en parameter för att skapa. Se [Sorteringar](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Efterlevnadsnivåer

- Kompatibilitetsnivåerna som stöds är 100, 110, 120, 130, 140 och 150.
- Kompatibilitetsnivåer under 100 stöds inte.
- Standardkompatibilitetsnivån för nya databaser är 140. För återställda databaser förblir kompatibilitetsnivån oförändrad om den var 100 och högre.

Se [ÄNDRA KOMPATIBILITETSNIVÅ FÖR DATABAS](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Databasspegling

Databasspegling stöds inte.

- `ALTER DATABASE SET PARTNER`och `SET WITNESS` alternativ stöds inte.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`stöds inte.

Mer information finns i [ALTER DATABASE SET PARTNER och ANGE VITTNE](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) och SKAPA [SLUTPUNKT ... För DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Alternativ för databas

- Flera loggfiler stöds inte.
- Minnesobjekt stöds inte på tjänstnivån Allmänt ändamål. 
- Det finns en gräns på 280 filer per allmänt syfte-instans, vilket innebär högst 280 filer per databas. Både data- och loggfiler på nivån Allmänt syfte räknas in i den här gränsen. [Den affärskritiska nivån stöder 32 767 filer per databas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Databasen får inte innehålla filgrupper som innehåller filströmsdata. Återställningen misslyckas om `FILESTREAM` .bak innehåller data. 
- Varje fil placeras i Azure Blob-lagring. IO och dataflöde per fil beror på storleken på varje enskild fil.

#### <a name="create-database-statement"></a>SKAPA DATABASsats

Följande begränsningar gäller `CREATE DATABASE`för:

- Det går inte att definiera filer och filgrupper. 
- Alternativet `CONTAINMENT` stöds inte. 
- `WITH`alternativ stöds inte. 
   > [!TIP]
   > Som en lösning kan `ALTER DATABASE` `CREATE DATABASE` du använda efter för att ange databasalternativ för att lägga till filer eller ange inneslutning. 

- Alternativet `FOR ATTACH` stöds inte.
- Alternativet `AS SNAPSHOT OF` stöds inte.

Mer information finns i [SKAPA DATABAS](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>ÄNDRA DATABAS-sats

Vissa filegenskaper kan inte anges eller ändras:

- Det går inte att anges en `ALTER DATABASE ADD FILE (FILENAME='path')` filsökväg i T-SQL-uttrycket. Ta `FILENAME` bort från skriptet eftersom en hanterad instans automatiskt placerar filerna. 
- Det går inte att ändra ett `ALTER DATABASE` filnamn med hjälp av uttrycket.

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

Mer information finns i [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Aktivera och inaktivera SQL Server Agent stöds för närvarande inte i hanterad instans. SQL Agent körs alltid.
- SQL Server Agent-inställningarna är skrivskyddade. Proceduren `sp_set_agent_properties` stöds inte i hanterade instanser. 
- Jobb
  - T-SQL-jobbsteg stöds.
  - Följande replikeringsjobb stöds:
    - Läsare av transaktionslogg
    - Ögonblicksbild
    - Distributör
  - SSIS-jobbsteg stöds.
  - Andra typer av jobbsteg stöds för närvarande inte:
    - Steget för sammanfogning av replikeringsjobb stöds inte. 
    - Köläsare stöds inte. 
    - Kommandoskal stöds ännu inte.
  - Hanterade instanser kan inte komma åt externa resurser, till exempel nätverksresurser via robocopy. 
  - SQL Server Analysis Services stöds inte.
- Meddelanden stöds delvis.
- E-postavisering stöds, även om det krävs att du konfigurerar en databaspostprofil. SQL Server Agent kan bara använda en databaspostprofil och den måste anropas `AzureManagedInstance_dbmail_profile`. 
  - Personsökare stöds inte.
  - NetSend stöds inte.
  - Aviseringar stöds ännu inte.
  - Proxyservrar stöds inte.
- EventLog stöds inte.

Följande SQL Agent-funktioner stöds för närvarande inte:

- Proxyservrar
- Schemalägga jobb på en inaktiv processor
- Aktivera eller inaktivera en agent
- Aviseringar

Information om SQL Server Agent finns i [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabeller

Följande tabelltyper stöds inte:

- [Filestream](/sql/relational-databases/blob/filestream-sql-server)
- [ARKIVTABELL](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql) (Polybas)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (stöds inte bara på nivån Allmänt syfte)

Information om hur du skapar och ändrar tabeller finns i [SKAPA TABELL](/sql/t-sql/statements/create-table-transact-sql) och [ÄNDRA TABELL](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funktioner

### <a name="bulk-insert--openrowset"></a>Bulkinsats / OPENROWSET

En hanterad instans kan inte komma åt filresurser och Windows-mappar, så filerna måste importeras från Azure Blob-lagring:

- `DATASOURCE`krävs i `BULK INSERT` kommandot medan du importerar filer från Azure Blob-lagring. Se [BULKINSATS](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`krävs i `OPENROWSET` funktionen när du läser innehållet i en fil från Azure Blob-lagring. Se [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`kan användas för att läsa data från andra Azure SQL-enskilda databaser, hanterade instanser eller SQL Server-instanser. Andra källor, till exempel Oracle-databaser eller Excel-filer stöds inte.

### <a name="clr"></a>Clr

En hanterad instans kan inte komma åt filresurser och Windows-mappar, så följande begränsningar gäller:

- Endast `CREATE ASSEMBLY FROM BINARY` stöds. Se [SKAPA ASSEM BLY FRÅN BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`stöds inte. Se [SKAPA SAMMANSÄTTNING FRÅN FIL](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`det går inte att referera till filer. Se [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>E-post för databaser (db_mail)
 - `sp_send_dbmail`det går inte @file_attachments att skicka bifogade filer med parametern. Lokalt filsystem och externa resurser eller Azure Blob Storage är inte tillgängliga från den här proceduren.
 - Se kända problem `@query` relaterade till parameter och autentisering.
 
### <a name="dbcc"></a>Dbcc

Odokumenterade DBCC-satser som är aktiverade i SQL Server stöds inte i hanterade instanser.

- Endast ett begränsat antal Globala spårningsflaggor stöds. Sessionsnivå `Trace flags` stöds inte. Se [Spåra flaggor](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) och [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) arbetar med det begränsade antalet globala spårningsflaggor.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) med alternativ REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST och REPAIR_REBUILD kan inte användas eftersom databasen inte `SINGLE_USER` kan ställas in i läge - se [ÄNDRA DATABASskillnader](#alter-database-statement). Potentiella databaskorruptioner hanteras av Azure-supportteamet. Kontakta Azure-supporten om du märker att databasen är skadad och som ska åtgärdas.

### <a name="distributed-transactions"></a>Distribuerade transaktioner

MSDTC och [elastiska transaktioner](sql-database-elastic-transactions-overview.md) stöds för närvarande inte i hanterade instanser.

### <a name="extended-events"></a>Extended Events

Vissa Windows-specifika mål för utökade händelser (XEvents) stöds inte:

- Målet `etw_classic_sync` stöds inte. Lagra `.xel` filer i Azure Blob-lagring. Se [etw_classic_sync mål](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Målet `event_file` stöds inte. Lagra `.xel` filer i Azure Blob-lagring. Se [event_file mål](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externa bibliotek

I databasen R och Python stöds externa bibliotek ännu inte. Se [SQL Server Machine Learning Services](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Filestream och FileTable

- Filströmsdata stöds inte.
- Databasen kan inte innehålla filgrupper `FILESTREAM` med data.
- `FILETABLE`stöds inte.
- Tabeller kan inte `FILESTREAM` ha typer.
- Följande funktioner stöds inte:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Mer information finns i [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) och [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Semantisk sökning i fulltext

[Semantisk sökning](/sql/relational-databases/search/semantic-search-sql-server) stöds inte.

### <a name="linked-servers"></a>Länkade servrar

Länkade servrar i hanterade instanser stöder ett begränsat antal mål:

- Mål som stöds är hanterade instanser, enskilda databaser och SQL Server-instanser. 
- Länkade servrar stöder inte distribuerade skrivbara transaktioner (MS DTC).
- Mål som inte stöds är filer, Analysis Services och andra RDBMS. Försök att använda inbyggd CSV-import `BULK INSERT` från `OPENROWSET` Azure Blob Storage med eller som ett alternativ för filimport.

Åtgärder

- Skrivtransaktioner mellan instanser stöds inte.
- `sp_dropserver`stöds för att släppa en länkad server. Se [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Funktionen `OPENROWSET` kan endast användas för att köra frågor på SQL Server-instanser. De kan antingen hanteras, lokalt eller i virtuella datorer. Se [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- Funktionen `OPENDATASOURCE` kan endast användas för att köra frågor på SQL Server-instanser. De kan antingen hanteras, lokalt eller i virtuella datorer. Endast `SQLNCLI` `SQLNCLI11` `SQLOLEDB` värdena och värdena stöds som leverantör. Ett exempel är `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Se [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Länkade servrar kan inte användas för att läsa filer (Excel, CSV) från nätverksresurserna. Försök att använda [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) eller [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) som läser CSV-filer från Azure Blob Storage. Spåra dessa begäranden på [feedbackobjekt för hanterade instanser](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Externa tabeller som refererar till filerna i HDFS- eller Azure Blob-lagring stöds inte. Information om PolyBase finns i [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikering

- Replikeringstyper för ögonblicksbilder och dubbelriktad riktning stöds. Koppla replikering, Peer-to-peer-replikering och uppdateringsbara prenumerationer stöds inte.
- [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md) är tillgängligt för offentlig förhandsversion på hanterad instans med vissa begränsningar:
    - Alla typer av replikeringsdeltagare (Utgivare, Distributör, Pull-prenumerant och Push-prenumerant) kan placeras på hanterade instanser, men utgivaren och distributören måste vara antingen både i molnet eller både lokalt.
    - Hanterade instanser kan kommunicera med de senaste versionerna av SQL Server. Mer information finns i matrisen versioner [som stöds.](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)
    - Transaktionsreplikering har några [ytterligare nätverkskrav](sql-database-managed-instance-transactional-replication.md#requirements).

Mer information om hur du konfigurerar transaktionsreplikering finns i följande självstudier:
- [Replikering mellan en MI-utgivare och prenumerant](replication-with-sql-database-managed-instance.md)
- [Replikering mellan en MI-utgivare, MI-distributör och SQL Server-prenumerant](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>RESTORE-sats 

- Syntax som stöds:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Syntaxen som inte stöds:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Källa: 
  - `FROM URL`(Azure Blob storage) är det enda alternativet som stöds.
  - `FROM DISK`/`TAPE`/backup-enhet stöds inte.
  - Säkerhetskopieringsuppsättningar stöds inte.
- `WITH`alternativ stöds inte, till `DIFFERENTIAL` exempel `STATS`ingen eller .
- `ASYNC RESTORE`: Återställningen fortsätter även om klientanslutningen går sönder. Om anslutningen har tagits bort `sys.dm_operation_status` kan du kontrollera om statusen för en återställningsåtgärd är status och för en CREATE- och DROP-databas. Se [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Följande databasalternativ anges eller åsidosätts och kan inte ändras senare: 

- `NEW_BROKER`om mäklaren inte är aktiverad i .bak-filen. 
- `ENABLE_BROKER`om mäklaren inte är aktiverad i .bak-filen. 
- `AUTO_CLOSE=OFF`om en databas i .bak-filen har `AUTO_CLOSE=ON`. 
- `RECOVERY FULL`om en databas i .bak-filen har `SIMPLE` eller `BULK_LOGGED` återställningsläge.
- En minnesoptimerad filgrupp läggs till och kallas XTP om den inte fanns i källfilen .bak. 
- Alla befintliga minnesoptimerade filgrupper har bytt namn till XTP. 
- `SINGLE_USER`och `RESTRICTED_USER` alternativ konverteras `MULTI_USER`till .

Begränsningar: 

- Säkerhetskopior av skadade databaser kan återställas beroende på vilken typ av skada, men automatiska säkerhetskopior kommer inte att tas förrän skadan är åtgärdad. Se till att `DBCC CHECKDB` du kör på `WITH CHECKSUM` källinstansen och använder säkerhetskopiering för att förhindra det här problemet.
- Återställning `.BAK` av en databas som innehåller alla begränsningar som beskrivs i det här dokumentet (till exempel `FILESTREAM` eller `FILETABLE` objekt) kan inte återställas vid hanterad instans.
- `.BAK`Filer som innehåller flera säkerhetskopior kan inte återställas. 
- `.BAK`filer som innehåller flera loggfiler kan inte återställas.
- Säkerhetskopior som innehåller databaser som är större än 8 TB, aktiva OLTP-objekt i minnet eller antal filer som skulle överstiga 280 filer per instans kan inte återställas vid en allmänt syfte-instans. 
- Säkerhetskopior som innehåller databaser som är större än 4 TB eller OLTP-objekt i minnet med den totala storleken som är större än den storlek som beskrivs i [resursgränser](sql-database-managed-instance-resource-limits.md) kan inte återställas i business critical-instansen.
Information om återställningssatser finns i [RESTORE-satser](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Samma begränsningar gäller för inbyggd point-in-time-återställning. Som ett exempel kan generaldatabas som är större än 4 TB inte återställas vid affärskritisk instans. Business Critical databas med I-minne OLTP-filer eller mer än 280 filer kan inte återställas vid allmänt ändamål instans.

### <a name="service-broker"></a>Servicemäklare

Tjänstmäklare med flera instanser stöds inte:

- `sys.routes`: Som en förutsättning måste du välja adressen från sys.routes. Adressen måste vara LOKAL på varje rutt. Se [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Du kan `CREATE ROUTE` inte `ADDRESS` använda `LOCAL`med annat än . Se [SKAPA RUTT](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Du kan `ALTER ROUTE` inte `ADDRESS` använda `LOCAL`med annat än . Se [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Lagrade procedurer, funktioner och utlösare

- `NATIVE_COMPILATION`stöds inte på nivån Allmänt syfte.
- Följande [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) alternativ stöds inte: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`stöds inte. Se [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell`stöds inte. Se [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`stöds inte, vilket `sp_addextendedproc`  inkluderar `sp_dropextendedproc`och . Se [Utökade lagrade procedurer](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`och `sp_detach_db` stöds inte. Se [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)och [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systemfunktioner och variabler

Följande variabler, funktioner och vyer ger olika resultat:

- `SERVERPROPERTY('EngineEdition')`returnerar värdet 8. Den här egenskapen identifierar unikt en hanterad instans. Se [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`Returnerar NULL eftersom förekomstens begrepp som det finns för SQL Server inte gäller för en hanterad instans. Se [SERVERPROPERTY("InstanceName")](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`returnerar ett fullständigt DNS-"connectable"-namn, till exempel my-managed-instance.wcus17662feb9ce98.database.windows.net. Se [@SERVERNAME@](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`returnerar ett fullständigt DNS-namn för `myinstance.domain.database.windows.net` "anslutningsbart", till exempel för egenskaperna "namn" och "data_source". Se [SYS. SERVRAR](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`returnerar NULL eftersom det finns ett servicebegrepp för SQL Server inte gäller för en hanterad instans. Se [@SERVICENAME@](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`stöds. Den returnerar NULL om Azure AD-inloggningen inte finns i sys.syslogins. Se [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID`stöds inte. Fel data returneras, vilket är ett tillfälligt känt problem. Se [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Miljöbegränsningar

### <a name="subnet"></a>Undernät
-  Du kan inte placera några andra resurser (till exempel virtuella datorer) i undernätet där du har distribuerat den hanterade instansen. Distribuera dessa resurser med ett annat undernät.
- Undernätet måste ha tillräckligt många tillgängliga [IP-adresser](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Minimum är 16, medan rekommendationen är att ha minst 32 IP-adresser i undernätet.
- [Tjänstslutpunkter kan inte associeras med den hanterade instansens undernät](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Kontrollera att alternativet för tjänstslutpunkter är inaktiverat när du skapar det virtuella nätverket.
- Antalet virtuella kärnor och typer av instanser som du kan distribuera i en region har vissa [begränsningar och begränsningar](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Det finns vissa [säkerhetsregler som måste tillämpas på undernätet](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET (PÅ ANDRA SÄTT)
- VNet kan distribueras med hjälp av Resource Model - Classic Model för VNet stöds inte.
- När en hanterad instans har skapats stöds inte att flytta den hanterade instansen eller det virtuella nätverket till en annan resursgrupp eller prenumeration.
- Vissa tjänster som App Service Environments, Logic-appar och hanterade instanser (som används för georeplikering, transaktionsreplikering eller via länkade servrar) kan inte komma åt hanterade instanser i olika regioner om deras virtuella nätverk är anslutna med [global peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Du kan ansluta till dessa resurser via ExpressRoute eller VNet-till-VNet via VNet Gateways.

### <a name="tempdb"></a>Tempdb

Den maximala filstorleken `tempdb` på kan inte vara större än 24 GB per kärna på en allmän nivå. Den `tempdb` maximala storleken på en affärskritisk nivå begränsas av instanslagringsstorleken. `Tempdb`loggfilsstorleken är begränsad till 120 GB på allmän nivå. Vissa frågor kan returnera ett fel om de behöver mer `tempdb` än 24 GB per kärna i eller om de producerar mer än 120 GB loggdata.

### <a name="msdb"></a>MSDB (MSDB)

Följande MSDB-scheman i hanterad instans måste ägas av deras respektive fördefinierade roller:

- Allmänna roller
  - TargetServersRole
- [Fasta databasroller](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail-roller:](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile)
  - DatabasMailUserRole
- [Roller för integrationstjänster:](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Om du ändrar de fördefinierade rollnamnen, schemanamnen och schemaägarna av kunder påverkas tjänstens normala funktion. Alla ändringar som görs i dessa återgår till de fördefinierade värdena så snart de har identifierats, eller senast vid nästa serviceuppdatering för att säkerställa normal servicedrift.

### <a name="error-logs"></a>Felloggar

En hanterad instans placerar utförlig information i felloggar. Det finns många interna systemhändelser som är inloggade i felloggen. Använd en anpassad procedur för att läsa felloggar som filtrerar bort vissa irrelevanta poster. Mer information finns i [hanterad instans – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) eller [hanterad instanstillägg(förhandsversion)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) för Azure Data Studio.

## <a name="next-steps"></a>Nästa steg

- Mer information om hanterade instanser finns i [Vad är en hanterad instans?](sql-database-managed-instance.md)
- En funktions- och jämförelselista finns i [Jämförelsen för funktionen Azure SQL Database](sql-database-features.md).
- För versionsuppdateringar och kända problemtillstånd finns i [versionsanteckningar för SQL Database](sql-database-release-notes.md)
- En snabbstart som visar hur du skapar en ny hanterad instans finns i [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).
