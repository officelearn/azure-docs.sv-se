---
title: Viktig information
description: Lär dig mer om de nya funktionerna och förbättringarna i Azure SQL Database-tjänsten och i Azure SQL Database-dokumentationen
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: sstein
ms.openlocfilehash: b677fd7fe2b14e1c42443478a887ddfa2481dfbf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011454"
---
# <a name="sql-database-release-notes"></a>Viktig information om SQL-databas

I den här artikeln visas SQL Database-funktioner som för närvarande förhandsgranskas offentligt. Uppdateringar och förbättringar av SQL Database finns i [uppdateringar av SQL Database-tjänsten](https://azure.microsoft.com/updates/?product=sql-database). Uppdateringar och förbättringar av andra Azure-tjänster finns i [Tjänstuppdateringar](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funktioner i offentlig förhandsversion

### <a name="single-database"></a>[Enkel databas](#tab/single-database)

| Funktion | Information |
| ---| --- |
| Nya hårdvarugenerationer i Fsv2-serien och M-serien| Mer information finns i [Maskinvarugenerationer](sql-database-service-tiers-vcore.md#hardware-generations).|
| Snabbare databasåterställning med enstaka databaser och elastiska pooler | Mer information finns i [Accelererad databasåterställning](sql-database-accelerated-database-recovery.md).|
|Ungefärlig antal distinkt|Information finns i [Ungefärlig antal distinkt](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Batchläge på Rowstore (under kompatibilitetsnivå 150)|Mer information finns i [Batch-läge på Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Dataidentifiering och -klassificering  |Mer information finns i [Azure SQL Database och SQL Data Warehouse dataidentifiering & klassificering](sql-database-data-discovery-and-classification.md).|
| Elastiska databasjobb | Information finns i [Skapa, konfigurera och hantera elastiska jobb](elastic-jobs-overview.md). |
| Elastiska frågor | Mer information finns i [Översikt över elastiska frågor](sql-database-elastic-query-overview.md). |
| Elastiska transaktioner | [Distribuerade transaktioner mellan molndatabaser](sql-database-elastic-transactions-overview.md). |
|Feedback om minnesbidrag (radläge) (under kompatibilitetsnivå 150)|Mer information finns i [Feedback om minnesbidrag (radläge)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Frågeredigeraren i Azure-portalen |Information finns [i Använda Azure-portalens SQL-frågeredigerare för att ansluta och fråga data](sql-database-connect-query-portal.md).|
| R-tjänster /maskininlärning med enstaka databaser och elastiska pooler |Mer information finns [i Machine Learning Services i Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|SQL-analys|Mer information finns i [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Tabell variabel uppskjuten sammanställning (under kompatibilitetsnivå 150)|Mer information finns i [Tabellvariabel uppskjuten kompilering](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Hanterad instans](#tab/managed-instance)

| Funktion | Information |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Instanspooler</a> | Ett bekvämt och kostnadseffektivt sätt att migrera mindre SQL-instanser till molnet. |
| <a href="https://aka.ms/managed-instance-aadlogins">Huvudnamn på Azure AD-server på instansnivå (inloggningar)</a> | Skapa inloggningar på servernivå med hjälp av <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER-satsen.</a> |
| [Transaktionell replikering](sql-database-managed-instance-transactional-replication.md) | Replikera ändringarna från tabellerna till andra databaser som placeras på hanterade instanser, enskilda databaser eller SQL Server-instanser eller uppdatera tabellerna när vissa rader ändras i andra hanterade instanser eller SQL Server-instans. Information finns [i Konfigurera replikering i en hanterad instansdatabas för Azure SQL Database](replication-with-sql-database-managed-instance.md). |
| Hotidentifiering |Information finns [i Konfigurera hotidentifiering i hanterad instans i Azure SQL Database](sql-database-managed-instance-threat-detection.md).|

---

## <a name="managed-instance---new-features-and-known-issues"></a>Hanterad instans - nya funktioner och kända problem

### <a name="managed-instance-h2-2019-updates"></a>Hanterade instans H2 2019-uppdateringar

- [Konfiguration av servicestödda undernät](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Ett säkert och bekvämt sätt att hantera nätkonfiguration där du styr datatrafik medan hanterad instans säkerställer oavbrutet flöde av hanteringstrafik
- [Transparent datakryptering (TDE) med Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) gör det möjligt att ta med dig ditt egennyckelscenario (BYOK) för dataskydd i vila och gör det möjligt för organisationer att separera hanteringsuppgifter för nycklar och data.
- [Med grupper med automatisk redundans](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) kan du replikera alla databaser från den primära instansen till en sekundär instans i en annan region.
- Konfigurera beteendet Hanterad instans med [globala spårningsflaggor](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Hanterade instans H1 2019-uppdateringar

Följande funktioner är aktiverade i Distributionsmodellen För hanterade instanser i H1 2019:
  - Stöd för prenumerationer med <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Azure månadskredit för Visual Studio-prenumeranter</a> och ökade [regionala gränser](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Stöd för <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 och SharePoint 2019 </a> och <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Skapa instanser med <a href="https://aka.ms/managed-instance-collation">servernivåsortering</a> och <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">valfri tidszon.</a>
  - Hanterade instanser skyddas nu med <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">inbyggd brandvägg</a>.
  - Konfigurera instanser för att använda [offentliga slutpunkter](sql-database-managed-instance-public-endpoint-configure.md), [Proxy åsidosätta](sql-database-connectivity-architecture.md#connection-policy) anslutningen för att få bättre nätverksprestanda, <a href="https://aka.ms/four-cores-sql-mi-update">4 virtuella kärnor på Gen5-maskinvarugenerering</a> eller <a href="https://aka.ms/managed-instance-configurable-backup-retention">Konfigurera kvarhållning av säkerhetskopiering upp till 35 dagar</a> för återställning av point-in-time. Långsiktig lagring av säkerhetskopiering (upp till 10 år) är fortfarande inte aktiverat så att du kan använda <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">säkerhetskopior endast för kopia</a> som ett alternativ.
  - Med nya funktioner kan du <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">geoaktivera databasen till ett annat datacenter med PowerShell</a>, [byta namn på databas](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), ta bort [virtuellt kluster](sql-database-managed-instance-delete-virtual-cluster.md).
  - Ny inbyggd [roll som bidragande instans](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) gör det möjligt att skilja tull (SoD) från att följa säkerhetsprinciper och följa företagsstandarder.
  - Hanterad instans är tillgänglig i följande Azure Government-regioner till GA (US Gov Texas, US Gov Arizona) samt i China North 2 och China East 2. Det är också tillgängligt i följande offentliga regioner: Australien Central, Australien Central 2, Brasilien South, Frankrike South, UAE Central, UAE North, Sydafrika North, Sydafrika Väst.

### <a name="known-issues"></a>Kända problem

|Problem  |Upptäckt datum  |Status  |Datum löst  |
|---------|---------|---------|---------|
|[Behörigheter för resursgruppen tillämpas inte på hanterad instans](#permissions-on-resource-group-not-applied-to-managed-instance)|Februari 2020|Har lösning||
|[Begränsning av manuell redundans via portal för redundansgrupper](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Har lösning||
|[SQL Agent-roller kräver explicita EXECUTE-behörigheter för icke-sysadmin-inloggningar](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Har lösning||
|[SQL Agent-jobb kan avbrytas av omstart av agentprocessen](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Ingen lösning|Mars 2020|
|[AAD-inloggningar och användare stöds inte i SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Nov 2019|Ingen lösning||
|[OLTP-minnesgränser i minnet tillämpas inte](#in-memory-oltp-memory-limits-are-not-applied)|Okt 2019|Har lösning||
|[Fel fel uppstod när du försökte ta bort en fil som inte är tom](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Okt 2019|Har lösning||
|[Ändra tjänstnivå och skapa instansåtgärder blockeras av pågående databasåterställning](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Har lösning||
|[Resurschef på tjänstnivå för affärskritisk kan behöva konfigureras om efter redundans](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Har lösning||
|[Dialogrutor för serviceutjämning över flera databaser måste initieras om efter uppgradering av tjänstnivå](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Augusti 2019|Har lösning||
|[Impersonification av Azure AD-inloggningstyper stöds inte](#impersonification-of-azure-ad-login-types-is-not-supported)|Jul 2019|Ingen lösning||
|[@queryparametern stöds inte i sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|April 2019|Ingen lösning||
|[Transaktionsreplikering måste konfigureras om efter geo-redundans](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mars 2019|Ingen lösning||
|[Tillfällig databas används under återställningsåtgärden](#temporary-database-is-used-during-restore-operation)||Har lösning||
|[TEMPDB struktur och innehåll återskapas](#tempdb-structure-and-content-is-re-created)||Ingen lösning||
|[Överskrider lagringsutrymme med små databasfiler](#exceeding-storage-space-with-small-database-files)||Har lösning||
|[GUID-värden som visas i stället för databasnamn](#guid-values-shown-instead-of-database-names)||Har lösning||
|[Felloggar sparas inte](#error-logs-arent-persisted)||Ingen lösning||
|[Transaktionsomfattning i två databaser inom samma instans stöds inte](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Har lösning||
|[CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Har lösning||
|Databaskonsekvensen har inte verifierats med DBCC CHECKDB efter återställning av databas från Azure Blob Storage.||Matchat|Nov 2019|
|Återställning av point-in-time-databas från affärskritisk nivå till allmän nivå lyckas inte om källdatabasen innehåller OLTP-objekt i minnet.||Matchat|Okt 2019|
|Databaspostfunktionen med externa e-postservrar (som inte är Azure) med säker anslutning||Matchat|Okt 2019|
|Inneslutna databaser som inte stöds i hanterad instans||Matchat|Augusti 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Behörigheter för resursgruppen tillämpas inte på hanterad instans

RBAC-roll för hanterad instansmedarbetare när den tillämpas på en resursgrupp (RG) tillämpas inte på hanterad instans och har ingen effekt.

**Lösning**: Roll för guiden Hanterad instansdeltagare för användare på prenumerationsnivå.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Begränsning av manuell redundans via portal för redundansgrupper

Om redundansgruppen sträcker sig över instanser i olika Azure-prenumerationer eller resursgrupper kan manuell redundans inte initieras från den primära instansen i redundansgruppen.

**Lösning**: Initiera redundans via portal från den geo-sekundära instansen.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Agent-roller kräver explicita EXECUTE-behörigheter för icke-sysadmin-inloggningar

Om icke-sysadmin-inloggningar läggs till i någon av [SQL Agent fasta databasroller](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)finns det ett problem där explicita EXECUTE-behörigheter måste beviljas till huvudbelagda procedurer för att dessa inloggningar ska fungera. Om det här problemet uppstår visas felmeddelandet "KÖRNINGsbehörigheten nekades på objektet <object_name> (Microsoft SQL Server, Fel: 229)" visas.

**Lösning:** När du lägger till inloggningar till någon av SQL Agent fasta databasroller: SQLAgentUserRole, SQLAgentReaderRole eller SQLAgentOperatorRole, för var och en av de inloggningar som läggs till dessa roller kör nedanstående T-SQL-skript för att uttryckligen bevilja EXECUTE-behörigheter till de lagrade procedurerna som anges.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL Agent-jobb kan avbrytas av omstart av agentprocessen

SQL Agent skapar en ny session varje gång jobbet startas, vilket gradvis ökar minnesförbrukningen. För att undvika att träffa den interna minnesgränsen som skulle blockera körning av schemalagda jobb, kommer Agent-processen att startas om när dess minnesförbrukning når tröskelvärdet. Det kan leda till att körningen av jobb som körs avbryts vid omstarten avbryts.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>OLTP-minnesgränser i minnet tillämpas inte

Business Critical service-nivå kommer inte att tillämpa [max minnesgränser för minnesoptimerade objekt](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) i vissa fall. Hanterad instans kan göra det möjligt för arbetsbelastningen att använda mer minne för OLTP-åtgärder i minnet, vilket kan påverka tillgängligheten och stabiliteten för instansen. OLTP-frågor i minnet som når gränserna kanske inte misslyckas omedelbart. Det här problemet kommer att åtgärdas snart. De frågor som använder mer OLTP-minne i minnet misslyckas tidigare om de når [gränserna](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Lösning:** [Övervaka OLTP-lagringsanvändning i minnet](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) med SQL Server Management [Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) för att säkerställa att arbetsbelastningen inte använder mer än tillgängligt minne. Öka de minnesgränser som beror på antalet virtuella kärnor eller optimera din arbetsbelastning så att den använder mindre minne.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Fel fel uppstod när du försökte ta bort en fil som inte är tom

SQL Server/Managed Instance [tillåter inte att användaren släpper en fil som inte är tom](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Om du försöker ta bort en `ALTER DATABASE REMOVE FILE` icke-tom `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` datafil med hjälp av utdrag returneras inte felet omedelbart. Hanterad instans fortsätter att försöka släppa filen och åtgärden misslyckas efter 30min med `Internal server error`.

**Lösning**: Ta bort innehållet i `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` filen med kommandot. Om detta är den enda filen i filgruppen måste du ta bort data från den tabell eller partition som är associerad med den här filgruppen innan du förminskar filen och eventuellt läsa in dessa data i en annan tabell/partition.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Ändra tjänstnivå och skapa instansåtgärder blockeras av pågående databasåterställning

Pågående `RESTORE` utdrag, migreringsprocess för datamigreringstjänsten och inbyggd tidsåterställning blockerar uppdatering av tjänstnivå eller ändrar storlek på den befintliga instansen och skapar nya instanser tills återställningsprocessen är klar. Återställningsprocessen blockerar dessa åtgärder på hanterade instanser och instanspooler i samma undernät där återställningsprocessen körs. Instanserna i instanspooler påverkas inte. Skapa eller ändra tjänstnivååtgärder misslyckas inte eller timeout - de fortsätter när återställningsprocessen har slutförts eller avbrutits.

**Lösning**: Vänta tills återställningsprocessen är klar eller avbryt återställningsprocessen om åtgärden för att skapa eller uppdatera tjänstnivå har högre prioritet.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resurschef på tjänstnivå för affärskritisk kan behöva konfigureras om efter redundans

[Resurschefsfunktionen](/sql/relational-databases/resource-governor/resource-governor) som gör att du kan begränsa de resurser som tilldelats användararbetsbelastningen kan felaktigt klassificera vissa användararbetsbelastning efter redundans eller användarinitierad ändring av tjänstnivå (till exempel ändringen av max vCore eller max instanslagringsstorlek).

**Lösning:** Kör `ALTER RESOURCE GOVERNOR RECONFIGURE` regelbundet eller som en del av SQL Agent-jobbet som kör SQL-aktiviteten när instansen startar om du använder [resursguvernör](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Dialogrutor för serviceutjämning över flera databaser måste initieras om efter uppgradering av tjänstnivå

Dialogrutor för serviceutjämning över flera databaser slutar leverera meddelandena till tjänsterna i andra databaser efter åtgärden ändra tjänstnivå. Meddelandena går **inte förlorade** och de finns i avsändatorkön. Om du vill ändra virtuella kärnor eller instanslagringsstorlek i Hanterad instans ändras `service_broke_guid` värdet i [vyn sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) för alla databaser. Alla `DIALOG` som skapas med [BEGIN DIALOG-satsen](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) som refererar till Service Brokers i andra databaser kommer att sluta leverera meddelanden till måltjänsten.

**Lösning:** Stoppa alla aktiviteter som använder dialogkonversationer i tjänstutjämningen över flera databaser innan du uppdaterar tjänstnivån och initierar om dem efteråt. Om det finns återstående meddelanden som inte levereras efter att tjänstnivåändringen läst meddelandena från källkön och skickar dem igen till målkön.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Impersonification av Azure AD-inloggningstyper stöds inte

Personifiering `EXECUTE AS USER` `EXECUTE AS LOGIN` med eller följande AAD-huvudnamn stöds inte:
-   Alias AAD-användare. Följande fel returneras i `15517`det här fallet .
- AAD loggar in och användare baserat på AAD-program eller tjänsthuvudnamn. Följande fel returneras i det `15517` `15406`här fallet och .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparametern stöds inte i sp_send_db_mail

Parametern `@query` i [sp_send_db_mail-proceduren](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) fungerar inte.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Transaktionsreplikering måste konfigureras om efter geo-redundans

Om Transaktionell replikering är aktiverat i en databas i en grupp för automatisk redundans måste administratören för hanterade instans rensa alla publikationer på den gamla primära och konfigurera om dem på den nya primärt när en redundans till en annan region inträffar. Mer information [finns i Replikering.](sql-database-managed-instance-transact-sql-information.md#replication)

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>AAD-inloggningar och användare stöds inte i SSDT

SQL Server Data Tools stöder inte fullt ut Azure Active directory-inloggningar och användare.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tillfällig databas används under återställningsåtgärden

När en databas återställs på Hanterad instans skapas först en tom databas med önskat namn för att allokera namnet på instansen. Efter en tid kommer databasen att tas bort och återställning av den faktiska databasen kommer att startas. Databasen som är i *återställa tillstånd* kommer tillfälligt att ha ett slumpmässigt GUID-värde i stället för namn. Det tillfälliga namnet ändras till önskat `RESTORE` namn som anges i utdrag när återställningsprocessen är klar. I den inledande fasen kan användaren komma åt den tomma databasen och till och med skapa tabeller eller läsa in data i den här databasen. Den här tillfälliga databasen tas bort när återställningstjänsten startar den andra fasen.

**Lösning**: Öppna inte databasen som du återställer förrän återställningen är slutförd.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB struktur och innehåll återskapas

Databasen `tempdb` är alltid uppdelad i 12 datafiler och filstrukturen kan inte ändras. Det går inte att ändra den maximala storleken per `tempdb`fil och det går inte att lägga till nya filer i . `Tempdb`skapas alltid igen som en tom databas när instansen startar `tempdb` eller växlar över, och alla ändringar som görs i bevaras inte.

### <a name="exceeding-storage-space-with-small-database-files"></a>Överskrider lagringsutrymme med små databasfiler

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`och `RESTORE DATABASE` satser kan misslyckas eftersom instansen kan nå Azure Storage-gränsen.

Varje hanterad instans för allmänt syfte har upp till 35 TB lagringsutrymme som reserverats för Azure Premium-diskutrymme. Varje databasfil placeras på en separat fysisk disk. Diskstorlekar kan vara 128 GB, 256 GB, 512 GB, 1 TB eller 4 TB. Oanvänt utrymme på disken debiteras inte, men den totala summan av Azure Premium Disk storlekar kan inte överstiga 35 TB. I vissa fall kan en hanterad instans som inte behöver totalt 8 TB överskrida azure-gränsen på 35 TB på grund av intern fragmentering.

En hanterad instans för allmänt bruk kan till exempel ha en stor fil som är 1,2 TB stor placerad på en 4 TB-disk. Det kan också ha 248 filer med 1 GB storlek vardera som placeras på separata 128 GB diskar. I det här exemplet:

- Den totala allokerade disklagringsstorleken är 1 x 4 TB + 248 x 128 GB = 35 TB.
- Det totala reserverade utrymmet för databaser i instansen är 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Det här exemplet illustrerar att en hanterad instans under vissa omständigheter, på grund av en specifik distribution av filer, kan nå gränsen på 35 TB som är reserverad för en ansluten Azure Premium Disk när du kanske inte förväntar dig det.

I det här exemplet fortsätter befintliga databaser att fungera och kan växa utan problem så länge nya filer inte läggs till. Det går inte att skapa eller återställa nya databaser eftersom det inte finns tillräckligt med utrymme för nya hårddiskar, även om den totala storleken på alla databaser inte når instansstorleksgränsen. Felet som returneras i så fall är inte klart.

Du kan [identifiera antalet återstående filer](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) med hjälp av systemvyer. Om du når den här gränsen kan du försöka [tömma och ta bort några av de mindre filerna med hjälp av DBCC SHRINKFILE-satsen](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) eller växla till den [affärskritiska nivån, som inte har den här gränsen](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>GUID-värden som visas i stället för databasnamn

I flera systemvyer, prestandaräknare, felmeddelanden, XEvents och felloggposter visas GUID-databasidentifierare i stället för de faktiska databasnamnen. Lita inte på dessa GUID-identifierare eftersom de ersätts med faktiska databasnamn i framtiden.

**Lösning**: Använd vyn sys.databases för att matcha det faktiska databasnamnet från det fysiska databasnamnet, angivet i form av GUID-databasidentifierare

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Felloggar sparas inte

Felloggar som är tillgängliga i hanterade instans sparas inte och deras storlek ingår inte i den maximala lagringsgränsen. Felloggar kan raderas automatiskt om redundans inträffar. Det kan finnas luckor i fellogghistoriken eftersom hanterad instans har flyttats flera gånger på flera virtuella datorer.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transaktionsomfattning i två databaser inom samma instans stöds inte

Klassen `TransactionScope` i .NET fungerar inte om två frågor skickas till två databaser inom samma instans under samma transaktionsomfång:

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

Även om den här koden fungerar med data i samma instans krävs MSDTC.

**Lösning:** Använd [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) om du vill använda en annan databas i en anslutningskontext i stället för att använda två anslutningar.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-moduler och länkade servrar kan ibland inte referera till en lokal IP-adress

CLR-moduler som placeras i en hanterad instans och länkade servrar eller distribuerade frågor som refererar till en aktuell instans kan ibland inte lösa IP-adressen för en lokal instans. Det här felet är ett tillfälligt problem.

**Lösning:** Använd kontextanslutningar i en CLR-modul om möjligt.

## <a name="updates"></a>Uppdateringar

En lista över uppdateringar och förbättringar i SQL Database finns i uppdateringar av [SQL Database-tjänsten](https://azure.microsoft.com/updates/?product=sql-database).

Uppdateringar och förbättringar av alla Azure-tjänster finns i [Tjänstuppdateringar](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Bidra med innehåll

Information om hur du bidrar till Azure SQL Database-dokumentationen finns i [docs contributor guide](https://docs.microsoft.com/contribute/).
