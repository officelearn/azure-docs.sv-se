---
title: Viktig information
description: Lär dig mer om de nya funktionerna och förbättringarna i Azure SQL Databases tjänsten och i Azure SQL Database-dokumentationen
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sstein
ms.openlocfilehash: c3dc5b26435f6d876e5eaea943e359055018913b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82201320"
---
# <a name="sql-database-release-notes"></a>Viktig information om SQL Database

Den här artikeln innehåller SQL Database funktioner som för närvarande finns i en offentlig för hands version. SQL Database uppdateringar och förbättringar finns i [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database). Uppdateringar och förbättringar av andra Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funktioner i offentlig för hands version

### <a name="single-database"></a>[Enkel databas](#tab/single-database)

| Funktion | Information |
| ---| --- |
| Nya maskin varu generationer i Fsv2-serien och M-serien| Mer information finns i [maskin varu generationer](sql-database-service-tiers-vcore.md#hardware-generations).|
| Accelererad databas återställning med enkla databaser och elastiska pooler | Mer information finns i [accelererad databas återställning](sql-database-accelerated-database-recovery.md).|
|Ungefärligt antal distinkta|Mer information finns i [Ungefärlig Count DISTINCT](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Batch-läge på Rowstore (under kompatibilitetsnivå 150)|Mer information finns i [batch-läge på Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Dataidentifiering och -klassificering  |Mer information finns i [Azure SQL Database och SQL Data Warehouse data identifiering & klassificering](sql-database-data-discovery-and-classification.md).|
| Elastiska databasjobb | Mer information finns i [skapa, konfigurera och hantera elastiska jobb](elastic-jobs-overview.md). |
| Elastiska frågor | Mer information finns i [Översikt över elastiska frågor](sql-database-elastic-query-overview.md). |
| Elastiska transaktioner | [Distribuerade transaktioner över moln databaser](sql-database-elastic-transactions-overview.md). |
|Feedback om minnes tilldelning (rad läge) (under kompatibilitetsnivå 150)|Mer information finns i [minnes beviljande feedback (rad läge)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Frågeredigeraren i Azure Portal |Mer information finns i [använda Azure Portal SQL-Frågeredigeraren för att ansluta och fråga efter data](sql-database-connect-query-portal.md).|
| R Services/Machine Learning med enkla databaser och elastiska pooler |Mer information finns i [Machine Learning Services i Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|SQL-analys|Mer information finns i [Azure SQL-analys](../azure-monitor/insights/azure-sql.md).|
|Uppskjuten tabell variabel (under kompatibilitetsnivå 150)|Mer information finns i [tabell variabel uppskjuten kompilering](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Hanterad instans](#tab/managed-instance)

| Funktion | Information |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Instanspooler</a> | Ett bekvämt och kostnads effektivt sätt att migrera mindre SQL-instanser till molnet. |
| <a href="https://aka.ms/managed-instance-aadlogins">Azure AD server-huvudobjekt på instans nivå (inloggningar)</a> | Skapa inloggningar på server nivå med hjälp av instruktionen <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning från extern provider</a> . |
| [Transaktionell replikering](sql-database-managed-instance-transactional-replication.md) | Replikera ändringarna från dina tabeller till andra databaser som har placerats på hanterade instanser, enskilda databaser eller SQL Server instanser eller uppdatera tabeller när vissa rader ändras i andra hanterade instanser eller SQL Server instans. Mer information finns i [Konfigurera replikering i en Azure SQL Database Hanterad instans databas](replication-with-sql-database-managed-instance.md). |
| Hotidentifiering |Mer information finns i [Konfigurera hot identifiering i Azure SQL Database Hanterad instans](sql-database-managed-instance-threat-detection.md).|
| Långsiktig kvarhållning av säkerhetskopior | Mer information finns i [Konfigurera långsiktig säkerhets kopiering i Azure SQL Database Hanterad instans](sql-database-managed-instance-long-term-backup-retention-configure.md), som för närvarande finns i begränsad offentlig för hands version. | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Hanterad instans – nya funktioner och kända problem

### <a name="managed-instance-h2-2019-updates"></a>Hanterad instans H2 2019 uppdateringar

- [Konfiguration av tjänstens konfiguration för under nätet](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Ett säkert och bekvämt sätt att hantera under näts konfiguration där du styr data trafik medan hanterad instans säkerställer det oavbrutna flödet av hanterings trafik
- [Med transparent data kryptering (TDE) med Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) kan du använda BYOK-scenariot () för att skydda data i vila och gör det möjligt för organisationer att separera hanterings uppgifter för nycklar och data.
- Med [grupper för automatisk redundans](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) kan du replikera alla databaser från den primära instansen till en sekundär instans i en annan region.
- Konfigurera din hanterade instans beteende med [globala spårnings flaggor](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Hanterad instans H1 2019-uppdateringar

Följande funktioner är aktiverade i distributions modellen för hanterade instanser i H1 2019:
  - Support för prenumerationer med <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Azures månatliga kredit för Visual Studio-prenumeranter</a> och ökade [regionala gränser](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Stöd för <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 och SharePoint 2019 </a> och <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Skapa instanser med <a href="https://aka.ms/managed-instance-collation">sortering på server nivå</a> och <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">tidszon</a> som du väljer.
  - Hanterade instanser skyddas nu med <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">inbyggd brand vägg</a>.
  - Konfigurera instanser för att använda [offentliga slut punkter](sql-database-managed-instance-public-endpoint-configure.md), en [proxy-Åsidosätt](sql-database-connectivity-architecture.md#connection-policy) anslutning för att få bättre nätverks prestanda, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuella kärnor på Gen5-maskinvarukompatibilitet</a> eller <a href="https://aka.ms/managed-instance-configurable-backup-retention">Konfigurera kvarhållning av säkerhets kopior upp till 35 dagar</a> för återställning vid tidpunkter. [Långsiktig kvarhållning av säkerhets kopior](sql-database-long-term-retention.md#managed-instance-support) (upp till 10 år) är för närvarande en begränsad offentlig för hands version.  
  - Med nya funktioner kan du <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geo-återställa databasen till ett annat data Center med hjälp av PowerShell</a>, [byta namn på databas](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [ta bort virtuellt kluster](sql-database-managed-instance-delete-virtual-cluster.md).
  - Den nya inbyggda [rollen instans deltagar roll](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) möjliggör separering av tull (SOD) efterlevnad av säkerhets principer och efterlevnad av företags standarder.
  - Hanterad instans är tillgänglig i följande Azure Government regioner till GA (US Gov, Texas, US Gov, Arizona) och i Kina, norra 2 och Kina, östra 2. Den är också tillgänglig i följande offentliga regioner: Australien, centrala, Australien, centrala, södra Frankrike, södra Förenade Arabemiraten Central, Förenade Arabemiraten Nord, Sydafrika, norra, södra Afrika, västra.

### <a name="known-issues"></a>Kända problem

|Problem  |Datum identifierat  |Status  |Åtgärds datum  |
|---------|---------|---------|---------|
|[Behörigheter för resurs gruppen har inte tillämpats på den hanterade instansen](#permissions-on-resource-group-not-applied-to-managed-instance)|Feb 2020|Har en lösning||
|[Begränsning av manuell redundans via portalen för failover-grupper](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Har en lösning||
|[SQL Agent-roller behöver explicit kör-behörighet för icke-sysadmin-inloggningar](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Har en lösning||
|[SQL Agent-jobb kan avbrytas efter omstart av agent processen](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Matchat|Mar 2020|
|[AAD-inloggningar och användare stöds inte i SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Ingen lösning||
|[Minnes gränser för minnes intern OLTP tillämpas inte](#in-memory-oltp-memory-limits-are-not-applied)|Okt 2019|Har en lösning||
|[Ett fel fel returnerades vid försök att ta bort en fil som inte är tom](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Okt 2019|Har en lösning||
|[Ändra tjänst nivå och skapa instans åtgärder blockeras av pågående databas återställning](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Har en lösning||
|[Resource Governor på Affärskritisk tjänst nivå kan behöva konfigureras om efter en redundansväxling](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Har en lösning||
|[Service Broker dialog rutor mellan databaser måste initieras igen efter uppgraderingen av service nivå](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Aug 2019|Har en lösning||
|[Impersonification av Azure AD-inloggnings typer stöds inte](#impersonification-of-azure-ad-login-types-is-not-supported)|Jul 2019|Ingen lösning||
|[@queryparametern stöds inte i sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|Ingen lösning||
|[Transaktionsreplikering måste konfigureras om efter GEO-redundans](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Ingen lösning||
|[Tillfällig databas används under återställnings åtgärden](#temporary-database-is-used-during-restore-operation)||Har en lösning||
|[TEMPDB-strukturen och innehållet har skapats på nytt](#tempdb-structure-and-content-is-re-created)||Ingen lösning||
|[Överskrida lagrings utrymme med små databasfiler](#exceeding-storage-space-with-small-database-files)||Har en lösning||
|[GUID-värden som visas i stället för databas namn](#guid-values-shown-instead-of-database-names)||Har en lösning||
|[Fel loggarna är inte beständiga](#error-logs-arent-persisted)||Ingen lösning||
|[Transaktions omfånget på två databaser inom samma instans stöds inte](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Har en lösning|Mar 2020|
|[CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Har en lösning||
|Databasens konsekvens kontrol leras inte med DBCC CHECKDB efter återställningen av databasen från Azure Blob Storage.||Matchat|Nov 2019|
|Det går inte att återställa punkt-i-tid från Affärskritisk nivå till Generell användning nivå om käll databasen innehåller minnesbaserade OLTP-objekt.||Matchat|Okt 2019|
|Database Mail funktion med externa (icke-Azure) e-postservrar som använder säker anslutning||Matchat|Okt 2019|
|Inneslutna databaser stöds inte i hanterade instanser||Matchat|Aug 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Behörigheter för resurs gruppen har inte tillämpats på den hanterade instansen

Hanterad instans rollen RBAC-roll när den tillämpas på en resurs grupp (RG) används inte för en hanterad instans och har ingen effekt.

**Lösning**: Konfigurera en hanterad instans deltagar roll för användare på prenumerations nivå.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Begränsning av manuell redundans via portalen för failover-grupper

Om redundansväxlingen sträcker sig över instanser i olika Azure-prenumerationer eller resurs grupper kan inte manuell redundans initieras från den primära instansen i gruppen för redundans.

**Lösning**: starta redundans via portalen från den geo-sekundära instansen.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Agent-roller behöver explicit kör-behörighet för icke-sysadmin-inloggningar

Om icke-sysadmin-inloggningar har lagts till i någon av de [fasta databas rollerna för SQL-agenten](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), finns det ett problem där EXPLICITa körnings behörigheter måste beviljas till de Master-lagrade procedurerna för att dessa inloggningar ska fungera. Om det här problemet uppstår nekades fel meddelandet "KÖRNINGs behörigheten för objektet <object_name> (Microsoft SQL Server, fel: 229)" visas.

**Lösning**: när du lägger till inloggningar till någon av de fasta databas rollerna för SQL Agent: SQLAgentUserRole, SQLAgentReaderRole eller SQLAgentOperatorRole, för varje inloggnings objekt som lagts till i dessa roller, kör skriptet under T-SQL för att uttryckligen bevilja kör behörighet till de lagrade procedurerna.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL Agent-jobb kan avbrytas efter omstart av agent processen

**(Löst i mars 2020)** SQL-agenten skapar en ny session varje gång jobbet startas, vilket gradvis ökar minnes användningen. För att undvika att den interna minnes gränsen uppnås, vilket skulle blockera körning av schemalagda jobb, startas agent processen om när minnes användningen når tröskelvärdet. Det kan leda till avbrott i körningen av jobb som körs vid tidpunkten för omstart.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Minnes gränser för minnes intern OLTP tillämpas inte

Affärskritisk tjänst nivån kommer inte att tillämpa [högsta minnes gränser för minnesoptimerade objekt](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) i vissa fall. Den hanterade instansen kan göra att arbets belastningen kan använda mer minne för minnes intern OLTP-åtgärder, vilket kan påverka instansens tillgänglighet och stabilitet. InMemory OLTP-frågor som når gränserna kanske inte kommer att Miss genast. Det här problemet kommer snart att åtgärdas. Frågor som använder mer minnes-till-minne OLTP-minne kommer att Miss Miss kort om de når [gränserna](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Lösning:** [övervaka minnes intern OLTP-lagring](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) med [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) för att säkerställa att arbets belastningen inte använder mer än tillgängligt minne. Öka de minnes gränser som beror på antalet virtuella kärnor eller optimera arbets belastningen för att använda mindre minne.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Ett fel fel returnerades vid försök att ta bort en fil som inte är tom

SQL Server/hanterad instans [tillåter inte att användaren släpper en fil som inte är tom](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Om du försöker ta bort en icke-tom datafil med hjälp `ALTER DATABASE REMOVE FILE` av instruktionen returneras `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` inte felet omedelbart. Den hanterade instansen fortsätter att försöka släppa filen och åtgärden kommer att Miss sen `Internal server error`efter 30 min med.

**Lösning**: ta bort innehållet i filen med `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` kommandot. Om det här är den enda filen i fil gruppen måste du ta bort data från tabellen eller partitionen som är kopplade till den här fil gruppen innan du krymper filen och eventuellt läsa in dessa data till en annan tabell/partition.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Ändra tjänst nivå och skapa instans åtgärder blockeras av pågående databas återställning

Kontinuerlig `RESTORE` instruktion, migrering av datamigrerings tjänsten och inbyggd tids återställning kommer att blockera uppdatering av tjänst nivån eller ändra storlek på den befintliga instansen och skapa nya instanser tills återställnings processen har slutförts. Med återställnings processen blockeras dessa åtgärder på hanterade instanser och instans-pooler i samma undernät där återställnings processen körs. Instanserna i instans pooler påverkas inte. Det går inte att skapa eller ändra åtgärder på tjänst nivå eller tids gräns – de fortsätter när återställnings processen har slutförts eller avbrutits.

**Lösning**: vänta tills återställningen har slutförts, eller Avbryt återställnings processen om åtgärden för att skapa eller uppdatera tjänst nivå har högre prioritet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor på Affärskritisk tjänst nivå kan behöva konfigureras om efter en redundansväxling

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) funktionen som gör att du kan begränsa de resurser som är tilldelade till användarens arbets belastning kan klassificera vissa användares arbets belastning efter redundansväxling eller en användarinitierad ändring av tjänst nivån (till exempel ändringen av Max vCore eller maximal instans lagrings storlek).

**Lösning**: kör `ALTER RESOURCE GOVERNOR RECONFIGURE` regelbundet eller som en del av SQL Agent-jobbet som kör SQL-aktiviteten när instansen startar om du använder [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Service Broker dialog rutor mellan databaser måste initieras igen efter uppgraderingen av service nivå

Service Broker dialog rutor mellan databaser slutar att leverera meddelanden till tjänsterna i andra databaser efter åtgärden ändra tjänst nivå. Meddelandena går **inte förlorade** och de finns i avsändar kön. Om du ändrar virtuella kärnor eller instans lagrings storlek i den hanterade instansen kommer `service_broke_guid` värdet i [sys. Databass](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) -vyn att ändras för alla databaser. Alla `DIALOG` skapade med [dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) instruktionen BEGIN som hänvisar till tjänst hanterare i andra databaser kommer att sluta leverera meddelanden till mål tjänsten.

**Lösning:** Stoppa alla aktiviteter som använder Service Broker dialog samtal över flera databaser innan du uppdaterar tjänst nivån och återinitierar dem igen. Om det finns återstående meddelanden som inte levereras efter ändringar i tjänst nivån läser du meddelandena från käll kön och skickar dem igen till målkön.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Impersonification av Azure AD-inloggnings typer stöds inte

Personifiering med `EXECUTE AS USER` eller `EXECUTE AS LOGIN` av följande AAD-huvudobjekt stöds inte:
-    AAD-användare med alias. Följande fel returneras i det här fallet `15517`.
- AAD-inloggningar och användare baserat på AAD-program eller tjänstens huvud namn. Följande fel returneras i det här fallet `15517` och. `15406`

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparametern stöds inte i sp_send_db_mail

`@query` Parametern i [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) proceduren fungerar inte.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Transaktionsreplikering måste konfigureras om efter GEO-redundans

Om transaktionell replikering har Aktiver ATS för en databas i en grupp för automatisk redundans, måste den hanterade instans administratören rensa alla publikationer på den gamla primära servern och konfigurera om dem på den nya primära servern efter en redundansväxling till en annan region. Se [replikering](sql-database-managed-instance-transact-sql-information.md#replication) för mer information.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>AAD-inloggningar och användare stöds inte i SSDT

SQL Server Data Tools har inte fullt stöd för inloggningar och användare i Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tillfällig databas används under återställnings åtgärden

När en databas återställs på den hanterade instansen skapar återställnings tjänsten först en tom databas med det önskade namnet för att allokera namnet på instansen. Efter en stund kommer den här databasen att tas bort och återställning av den faktiska databasen kommer att startas. Databasen som är i *återställnings* tillstånd kommer temporärt att ha ett slumpmässigt GUID-värde i stället för namn. Det tillfälliga namnet ändras till det önskade namnet i `RESTORE` instruktionen när återställnings processen har slutförts. I den inledande fasen kan användaren komma åt den tomma databasen och till och med skapa tabeller eller läsa in data i den här databasen. Den här tillfälliga databasen kommer att tas bort när återställnings tjänsten startar den andra fasen.

**Lösning**: få inte åtkomst till den databas som du återställer förrän du ser att återställningen har slutförts.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB-strukturen och innehållet har skapats på nytt

`tempdb` Databasen delas alltid upp i 12 datafiler och fil strukturen kan inte ändras. Den maximala storleken per fil kan inte ändras och nya filer kan inte läggas till i `tempdb`. `Tempdb`återskapas alltid som en tom databas när instansen startar eller växlar över och eventuella ändringar som görs i `tempdb` bevaras inte.

### <a name="exceeding-storage-space-with-small-database-files"></a>Överskrida lagrings utrymme med små databasfiler

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`-och `RESTORE DATABASE` -instruktioner kan Miss Miss kan uppstå på grund av att instansen kan uppnå gränsen för Azure Storage.

Varje Generell användning hanterad instans har upp till 35 TB lagring reserverat för Azure Premium-disk utrymme. Varje databas fil placeras på en separat fysisk disk. Disk storlekar kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Oanvänt utrymme på disken debiteras inte, men den totala summan av storleken på Azure Premium-diskar får inte överstiga 35 TB. I vissa fall kan en hanterad instans som inte behöver 8 TB totalt överskrida gränsen på 35 TB Azure på lagrings storleken på grund av intern fragmentering.

En Generell användning hanterad instans kan till exempel ha en stor fil som är 1,2 TB i storleken på en 4 TB-disk. Det kan också finnas 248 filer med en storlek på 1 GB som placeras på separata 128 GB-diskar. I det här exemplet:

- Den totala allokerade disk lagrings storleken är 1 x 4 TB + 248 x 128 GB = 35 TB.
- Det totala reserverade utrymmet för databaser på instansen är 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Det här exemplet illustrerar att under vissa omständigheter, på grund av en specifik distribution av filer, kan en hanterad instans uppnå gränsen på 35 TB som är reserverad för en ansluten Azure Premium-disk om du inte kan vänta på det.

I det här exemplet fortsätter befintliga databaser att fungera och kan växa utan problem så länge nya filer inte läggs till. Det går inte att skapa eller återställa nya databaser eftersom det inte finns tillräckligt med utrymme för nya disk enheter, även om den totala storleken på alla databaser inte når gränsen för instans storlek. Det fel som returneras i detta fall är inte klart.

Du kan [identifiera antalet återstående filer](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) med hjälp av systemvyer. Om du når den här gränsen kan du försöka att [tömma och ta bort några av de mindre filerna med hjälp av DBCC SHRINKFILE-instruktionen](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) eller växla till [Affärskritisks nivån, som inte har den här gränsen](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>GUID-värden som visas i stället för databas namn

Flera systemvyer, prestanda räknare, fel meddelanden, XEvents och fel logg poster visar GUID-databas identifierare i stället för de faktiska databas namnen. Använd inte dessa GUID-identifierare eftersom de ersätts med faktiska databas namn i framtiden.

**Lösning**: Använd sys. Databass-vyn för att matcha det faktiska databas namnet från det fysiska databas namnet, som anges i form av GUID-databas identifierare

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Fel loggarna är inte beständiga

Fel loggar som är tillgängliga i den hanterade instansen är inte bestående och deras storlek ingår inte i den maximala lagrings gränsen. Fel loggar kan raderas automatiskt om redundansväxlingen sker. Det kan finnas luckor i fel logg historiken eftersom den hanterade instansen har flyttats flera gånger på flera virtuella datorer.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transaktions omfånget på två databaser inom samma instans stöds inte

**(Löst i mars 2020)** `TransactionScope` Klassen i .net fungerar inte om två frågor skickas till två databaser inom samma instans under samma transaktions omfång:

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

**Lösning (behövs inte sedan mars 2020):** Använd [SQLConnection. ChangeDatabase (sträng)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) om du vill använda en annan databas i en anslutnings kontext i stället för att använda två anslutningar.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress

CLR-moduler placerade i en hanterad instans och länkade servrar eller distribuerade frågor som refererar till en aktuell instans kan ibland inte matcha IP-adressen för en lokal instans. Det här felet är ett tillfälligt problem.

**Lösning:** Använd kontext anslutningar i en CLR-modul om möjligt.

## <a name="updates"></a>Uppdateringar

En lista över SQL Database uppdateringar och förbättringar finns i [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database).

Uppdateringar och förbättringar av alla Azure-tjänster finns i [tjänste uppdateringar](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Bidra med innehåll

Information om hur du bidrar till Azure SQL Database-dokumentationen finns i [Guide för dokument bidrag](https://docs.microsoft.com/contribute/).
