---
title: Azure SQL Database hyperskala vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor kunder ställa om en Azure SQL-databas på tjänstnivån hyperskala - ofta kallas en storskalig databas.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 4f0691dcc8a0c4b0ebbfe26a49b75b86fa50f597
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236011"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Vanliga frågor och svar om Azure SQL hyperskala databaser

Den här artikeln innehåller svar på vanliga frågor och svar för kunder som överväger en databas i Azure SQL Database hyperskala-tjänstnivå och som ofta kallas en storskalig databas. Den här artikeln beskrivs de scenarier som har stöd för storskaliga och cross-funktionen-tjänster som är kompatibel med SQL Database hyperskala Allmänt.

- Den här vanliga frågor och svar är avsedd för läsare som har en kort förståelse för tjänstnivån hyperskala och vill ha sina specifika frågor och funderingar besvaras.
- Den här vanliga frågor och svar är inte avsedd att vara en handboken eller svara på frågor om hur du använder en SQL-databas hyperskala-databas. För det, rekommenderar vi att du refererar till den [Azure SQL Database hyperskala](sql-database-service-tier-hyperscale.md) dokumentation.

## <a name="general-questions"></a>Allmänna frågor

### <a name="what-is-a-hyperscale-database"></a>Vad är en storskalig databas

En storskalig databas är en Azure SQL-databas på tjänstnivån hyperskala som backas upp av storskaliga skalbar lagringsteknik. En storskalig databas har stöd för upp till 100 TB data och ger hög genomströmning och prestanda samt snabb skalning för att anpassa sig till arbetsbelastningskraven. Skalning är transparent för program, anslutning, frågebearbetning och så vidare arbete som andra SQL-databas.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Vilka typer av resurser och köpa modeller stöder hyperskala

Tjänstnivån hyperskala är endast tillgängliga för enskilda databaser med hjälp av den vCore-baserade inköpsmodellen i Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Hur skiljer sig tjänstnivån hyperskala från tjänstnivåer för generell användning och affärskritisk

Tjänsten vCore-baserade nivåer är främst differentierade baserat på tillgänglighet, lagringstyp och IOPs.

- Service-nivån generell användning passar de flesta företags arbetsbelastningar, med en belastningsutjämnad uppsättning beräknings- och lagringsalternativ där i/o-svarstid eller redundans gånger inte är prioriteten.
- Tjänstnivån hyperskala är optimerat för mycket stora arbetsbelastningar.
- Affärskritisk tjänstnivån är lämpligt för arbetsbelastningar där i/o-svarstiden är en prioritet.

| | Resurstyp | Allmän |  Storskalig | Verksamhetskritisk |
|:---|:---:|:---:|:---:|:---:|:---:|
| **Bäst för** |Alla|  De flesta företags arbetsbelastningar. Erbjudanden budgetera objektorienterad belastningsutjämnade beräknings- och lagringsalternativ. | Dataprogram med kapacitetskrav för stora mängder data och möjligheten att automatiskt skala lagringsutrymme och skala databearbetningen smidigt sätt. | OLTP-program med höga transaktionsintervall och lägsta svarstid i/o. Erbjuder högsta återhämtning för fel som använder flera isolerade repliker.|
|  **Resurstyp** ||Enkel databas / elastisk pool / hanterad instans | Enskild databas | Enkel databas / elastisk pool / hanterad instans |
| **Beräkna storlek**|Enkel databas / elastisk pool * | 1-80 virtuella kärnor | 1-80 virtuella kärnor * | 1-80 virtuella kärnor |
| |Hanterad instans | 8, 16, 24, 32, 40, 64, 80  vCores | Gäller inte | 8, 16, 24, 32, 40, 64, 80  vCores |
| **Lagringstyp** | Alla |Premium Fjärrlagring (per instans) | Ta bort kopplade lagring med lokal SSD-cache (per instans) | Supersnabb lokal SSD-lagring (per instans) |
| **Lagringsstorlek** | Enkel databas / elastisk pool | 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | Hanterad instans  | 32 GB – 8 TB | Gäller inte | 32 GB – 4 TB |
| **I/o-dataflöde** | Enkel databas ** | 500 IOPS per vCore med 7000 högsta IOPS | Okänd ännu | 5000 IOPS med 200 000 högsta IOPS|
| | Hanterad instans | Beror på storleken på filen | Gäller inte | Hanterad instans: Beror på storleken på filen|
|**Tillgänglighet**|Alla|1 repliken, ingen lässkala, inte lokal cachelagring | Flera repliker, upp till 15 lässkala, delvis lokalt cacheminne | 3 repliker, 1 lässkala, zonredundant HA fullständig lokal cache |
|**Säkerhetskopior**|Alla|RA-GRS 7-35 dagar (7 dagar som standard)| RA-GRS 7-35 dagar (7 dagar som standard), konstant tid point-in-time-återställning (PITR) | RA-GRS 7-35 dagar (7 dagar som standard) |

\* Elastiska pooler som inte stöds i hyperskala tjänstnivå

### <a name="who-should-use-the-hyperscale-service-tier"></a>Vem ska använda hyperskala tjänstnivån

Tjänstnivån hyperskala är främst avsett för kunder som har stora lokala SQL Server-databaser och vill att modernisera sina program genom att flytta till molnet eller för kunder som redan använder Azure SQL Database och vill utöka avsevärt risken för databasens storlek. Hyperskala är också avsedd för kunder som söker både hög prestanda och hög skalbarhet. Med storskaliga får du:

- Stöd för upp till 100 TB databasstorlek
- Snabb databassäkerhetskopior oavsett databasstorlek (säkerhetskopior baserat på ögonblicksbilder)
- Snabb databasen återställs oavsett databasstorlek (återställningar är från ögonblicksbilder)
- Högre dataflöde loggresultat i snabb transaktion commit gånger oavsett databasstorlek
- Läs skala ut till en eller flera skrivskyddade noder att avlasta Läs arbetsbelastningen och för frekvent metoderna.
- Snabb uppskalning av beräkning, tidpunkt konstant, ska vara mer kraftfulla att tillgodose det tunga arbetsbelastning och skala sedan ned, konstant tidpunkt. Detta påminner om att skala upp och ned mellan en P6 till en P11, till exempel men mycket snabbare eftersom det är inte en storlek på data igen.

### <a name="what-regions-currently-support-hyperscale"></a>Vilka regioner stöder för närvarande hyperskala

Azure SQL Database hyperskala-nivån är nu tillgänglig i de regioner som anges [översikt över Azure SQL Database hyperskala](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Jag kan skapa flera hyperskala databaser per logisk server

Ja. Mer information och gränser för hur många storskaliga databaser per logisk server, finns i [SQL Database-resursgränser för enkel och delade databaser på en logisk server](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristic-of-a-hyperscale-database"></a>Vad är prestandaegenskap för en storskalig databas

SQL Database hyperskala arkitekturen ger hög prestanda och dataflöde stöd för stora databasstorlekar. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Vad är skalbarheten i en storskalig databas

SQL Database hyperskala ger snabb skalbarhet på begäran för din arbetsbelastning.

- **Skala upp/ned**

  Med storskaliga, kan du skala upp beräkningsstorleken primära när det gäller resurser, t.ex. CPU, minne och skala sedan ned, konstant tidpunkt. Eftersom lagringen delas, är skala upp och skala ned inte en storlek på data igen.  
- **Skala In/ut**

  Med storskaliga får du också möjlighet att etablera en eller flera extra beräkningsnoder som du kan använda för att hantera din läsbegäranden. Det innebär att du kan använda dessa extra beräkningsnoder som skrivskyddade noder för att avlasta Läs arbetsbelastningen från den primära databearbetning. Förutom är skrivskyddad, dessa noder också fungera som hot standby-i händelse av en växling vid fel över från primärt.

  Etablering av var och en av dessa ytterligare compute-noder kan göras i konstant tid och är en online-åtgärd. Du kan ansluta till dessa extra skrivskyddad beräkningsnoder genom att ange den `ApplicationIntent` argumentet i anslutningssträngen till `read_only`. Alla anslutningar som markerats med `read-only` dirigeras automatiskt till en ytterligare skrivskyddad compute-noder.

## <a name="deep-dive-questions"></a>Djupdykning frågor

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Jag kan blanda hyperskala och enskilda databaser i en enda logisk server

Ja, det kan du.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Kräver hyperskala mitt program programmeringsmodell för att ändra

Nej, din programmeringsmodell förblir skick. Du använder anslutningssträngen som vanligt och andra vanliga lägen för att interagera med din Azure SQL-databas.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Vilka isoleringsnivåer för transaktioner som ska vara standard för SQL Database hyperskala databasen

På den primära noden är transaktionsisoleringsnivån RCSI (Läs allokerat ögonblicksbildisolering). På sekundära noder skrivskyddsskala är Isoleringsnivån ögonblicksbild.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Jag kan använda min lokala eller IaaS SQL Server-licens till SQL Database hyperskala

Ja, [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) är tillgänglig för hyperskala. Varje SQL Server Standard kärna mappa till 1 hyperskala virtuella kärnor. Varje SQL Server Enterprise-kärna kan mappa till 4 vCores i hyperskala. Du behöver inte en SQL-licens för sekundära repliker. Azure Hybrid-förmånen priset tillämpas automatiskt på lässkala (sekundära) repliker.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Vilka typer av arbetsbelastningar är SQL Database hyperskala utformat för

SQL Database hyperskala stöder alla SQL Server-arbetsbelastningar, men det är främst optimerat för OLTP. Du kan hämta Hybrid (HTAP) och analys (datamart) arbetsbelastningar samt.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Hur kan jag välja mellan Azure SQL Data Warehouse och SQL Database hyperskala

Om du är för närvarande kör interaktiva analysfrågor med hjälp av SQL Server som ett datalager, SQL Database hyperskala är ett bra alternativ eftersom du kan vara värd för relativt små informationslager (till exempel några TB upp till 10's TB) till en lägre kostnad och du kan migrera dina data  arbetsbelastningen för informationslager till SQL Database hyperskala utan kodändringar för T-SQL.

Om du kör dataanalys i stor skala med komplexa frågor och med Parallel Data Warehouse (PDW), Teradata eller andra massivt parallella Processor (MPP))-informationslager, SQL Data Warehouse kan vara det bästa valet.
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database storskaliga compute-frågor

### <a name="can-i-pause-my-compute-at-any-time"></a>Jag kan pausar mitt beräkning när som helst

Inte just nu, men du kan skala beräknings- och antal repliker ned till att minska kostnaderna under tider med låg belastning.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Jag kan etablera en beräkning med extra RAM arbetsbelastningens minnesintensiva

Nej. Du måste uppgradera till en högre beräkningsstorleken för att få mer RAM-minne. Mer information finns i [storlekar för lagring och beräkning i hyperskala](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Jag kan lägga till flera beräkningsnoder i olika storlekar

Nej.

### <a name="how-many-read-scale-replicas-are-supported"></a>Hur många lässkala repliker stöds

Hyperskala-databaser skapas med en lässkala replik (två repliker totalt) som standard. Du kan skala antalet skrivskyddade repliker mellan 0 och 4 med hjälp av den [Azure-portalen](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)...

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>För hög tillgänglighet, jag behöver för att etablera ytterligare beräkningsnoder

I hyperskala databaser tillhandahålls elasticiteten på lagringsnivå. Du behöver bara en replik som skydd. När beräkning repliken är nere kan skapas en ny replik automatiskt utan att förlora data.

Om det finns bara en replik, kan det ta lite tid att skapa den lokala cachen i den nya repliken efter en redundansväxling. Under fasen för cache-återskapning hämtar data direkt från sidan servrar, vilket resulterar i försämrade prestanda för IOPS och fråga i databasen.

För verksamhetskritiska appar som kräver hög tillgänglighet, bör du tillhandahålla minst 2 compute-noder, inklusive primära Beräkningsnoden (standard). På så sätt är det en hot standby-åtkomst vid en redundansväxling.

## <a name="data-size-and-storage-questions"></a>Data och frågor

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Vad är den maximala storlek stöds med SQL Database hyperskala

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Vad är storleken på transaktionsloggen med hyperskala

Transaktionsloggen med hyperskala är praktiskt taget oändlig. Du behöver inte bekymra dig om slut på loggutrymmet på ett system som har en hög log-genomströmning. Log generation frekvensen kan dock begränsade för kontinuerlig aggressiva arbetsbelastningar. Högsta varaktigt log generation hastighet är cirka 100 MB per sekund.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Mina temp db skala när min databas växer

Din `tempdb` databasen finns på lokal SSD-lagring och konfigureras baserat på beräkningsstorleken som du etablerar. Din `tempdb` är optimerade och ser ut för att ge högsta prestanda. Den `tempdb` storlek kan inte konfigureras och hanteras åt dig av undersystem för lagring.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Har Mina databasstorlek väx automatiskt eller måste jag hantera storleken för datafilerna

Databasens storlek växer automatiskt när du infoga/hämta mer data.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Vad är den minsta databasstorlek som har stöd för SQL Database hyperskala eller som börjar med

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>I vilka steg Mina databasens storlek växer

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Är lagring i hyperskala för SQL-databas för lokal eller fjärransluten

I hyperskala lagras datafiler i Azure standardlagring. Data cachelagras kraftigt på lokal SSD-lagring på virtuella datorer nära compute-noder. Compute-noder har dessutom ett cacheminne på lokal SSD och i minnet (Buffertpoolen och så vidare), för att minska frekvensen av data hämtas från fjärranslutna noder.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Jag kan hantera eller definiera filer eller filgrupper med hyperskala

Nej
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Jag kan etablera en hård gräns på datatillväxten för min databas

Nej

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Hur datafiler presenteras med SQL Database hyperskala

Datafilerna som styrs av sidan servrar. Eftersom storleken på data växer, läggs datafiler och associerade sidan servernoder.

### <a name="is-database-shrink-supported"></a>Stöds databasen förminskas

Nej

### <a name="is-database-compression-supported"></a>Database-komprimering är stöds

Ja

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Om jag har en stor tabell Mina tabelldata hämta spridda över flera datafiler

Ja. Datasidor som är associerade med en viss tabell kan få i flera datafiler, som alla ingår i samma filgruppen. SQL Server använder en [strategi för proportionell fyllning](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) att distribuera data via datafiler.

## <a name="data-migration-questions"></a>Frågor för migrering av data

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Jag kan flytta min befintliga Azure SQL-databaser på hyperskala tjänstnivån

Ja. Du kan flytta dina befintliga Azure SQL-databaser till hyperskala. Det här är en enkelriktad migrering. Du kan inte flytta databaser från hyperskala till en annan tjänstnivå. Vi rekommenderar att du gör en kopia av produktionsdatabaserna och migrera till hyperskala för bevis på koncept (POC).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Jag kan flytta min hyperskala-databaser till andra versioner

Nej. Du kan inte flytta en storskalig databas till en annan tjänstenivå för tillfället.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Jag förlora några funktioner eller funktioner efter migrering till hyperskala tjänstnivån

Ja. Vissa av Azure SQL Database-funktioner stöds inte i hyperskala ännu, inklusive men begränsat inte länge termen kvarhållning av säkerhetskopia. När du har migrerat dina databaser till hyperskala dessa funktioner att sluta fungera.  Vi förväntar oss dessa begränsningar att vara tillfällig.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Jag kan flytta min lokala SQL Server-databas eller SQL Server VM-databasen till hyperskala

Ja. Du kan använda alla befintliga migrering tekniker för att migrera till hyperskala, inklusive BACPAC Transaktionsreplikering, logiska inläsning av data. Se även de [Azure Database Migration Service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Vad är min stilleståndstid vid migrering från en lokal eller virtuell datormiljö till hyperskala och hur kan jag minimerar den

Avbrottstid är samma som stilleståndstid när du migrerar dina databaser till en enskild databas i Azure SQL Database. Du kan använda [Transaktionsreplikering](replication-to-sql-database.md#data-migration-scenario
) att minimera stilleståndstid vid migrering för databaser upp till några TB i storlek. För mycket stor databas (10 + TB), kan du migrera data med ADF, Spark eller andra tekniker för flytt av data.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Hur mycket tid skulle det tar att ta i X mängden data som SQL Database hyperskala

Hyperskala kan förbruka 100 MB/sek för nya/ändrade data.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Kan jag läsa data från blob-lagring och snabba load (till exempel Polybase och SQL Data Warehouse)

Du kan läsa data från Azure Storage och läsa in datainläsning i en storskalig databas (precis som du kan göra med en vanlig enkel databas). Polybase stöds inte för närvarande på Azure SQL Database. Du kan göra med Polybase [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) eller kör ett Spark-jobb [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) med den [Spark-anslutningsappen för SQL](sql-database-spark-connector.md). Massinfogning stöder i Spark-anslutningen till SQL.

Enkel återställning eller bulk loggning modellen stöds inte i hyperskala. Fullständiga återställningsmodellen krävs för att tillhandahålla hög tillgänglighet. Dock hyperskala ger bättre pris jämfört med en Azure SQL-databas på grund av den nya arkitekturen i loggen för inmatning av data.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>SQL Database hyperskala tillåter att etablera flera noder för att föra in stora mängder data

Nej. SQL Database hyperskala är en SMP-arkitektur och är inte en asymmetrisk Fleruppdragskörning eller en arkitektur med flera huvudservrar. Du kan bara skapa flera repliker att skala ut skrivskyddade arbetsbelastningar.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Vad är den äldsta SQL Server version kommer SQL Database hyperskala stöder migrering från

SQL Server 2005. Mer information finns i [migrera till en enkel databas eller en databas i pool](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Kompatibilitetsproblem, se [åtgärda kompatibilitetsproblem vid Databasmigrering](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Stöder migrering från andra datakällor, till exempel Aurora, MySQL, Oracle, DB2 och andra databasplattformar i hyperskala för SQL-databas

Ja. Kommer från olika datakällor utom kräver SQL Server logiska migrering. Du kan använda den [Azure Database Migration Service](../dms/dms-overview.md) för en logisk migrering.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Frågor om verksamheten affärskontinuitet och haveriberedskap återställning

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Vad serviceavtal tillhandahålls för en databas i hyperskala

Med standard primära plus 1 läsbara sekundära är serviceavtalet 99,95% tillgänglighet.  Med flera repliker går serviceavtalet upp till 99,99%.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Säkerhetskopior av databasen hanteras för mig av tjänsten Azure SQL Database

Ja

### <a name="how-often-are-the-database-backups-taken"></a>Hur ofta kommer säkerhetskopior av databasen

Det finns inga traditionella fullständig, differentiell och säkerhetskopieringar för SQL Database hyperskala databaser. I stället är det vanliga ögonblicksbilder av datafiler och log som genereras sparas bara skick för kvarhållningsperioden konfigurerade eller tillgängliga för dig.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>Pekar stöd för SQL Database hyperskala återställning till tidpunkt

Ja

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Vad är den mål för återställningspunkt (RPO) / återställning mål för återställningstid (RTO) med säkerhetskopiering/återställning i hyperskala för SQL-databas

Återställningspunktmålet är 0 min. RTO-mål är mindre än 10 minuter, oavsett storlek. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Påverkar säkerhetskopior av stora databaser beräkning prestanda på min primära

Nej. Säkerhetskopieringar som hanteras av underlagringssystemet och utnyttja ögonblicksbilder. De påverkar inte arbetsbelastningen på primärt användaren.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Jag kan utföra geo-återställning med en SQL-databas hyperskala-databas

Ja.  GEO-återställning stöds fullt ut.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Jag kan konfigurera Geo-replikering med SQL Database storskalig databas

Inte just nu.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Får Mina sekundära beräkningsnoder geo-replikerade med SQL Database hyperskala

Inte just nu.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Jag kan säkerhetskopiera en databas i SQL Database hyperskala och återställer dem till min lokala server eller SQL Server i virtuell dator

Nej. Lagringsformat för storskaliga databaser skiljer sig från traditionella SQL Server och du inte styra säkerhetskopior eller ha åtkomst till dem. Använda export-tjänsten eller Använd skript plus BCP för att få dina data från en databas i SQL Database hyperskala är.

## <a name="cross-feature-questions"></a>Mellan funktionen frågor

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Jag förlora några funktioner eller funktioner efter migrering till hyperskala tjänstnivån

Ja. Vissa av Azure SQL Database-funktioner stöds inte i hyperskala, inklusive men inte begränsat långsiktig kvarhållning av säkerhetskopia. När du har migrerat dina databaser till hyperskala dessa funktioner att sluta fungera.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Will Polybase fungerar med SQL Database hyperskala

Nej. Polybase stöds inte på Azure SQL Database.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Har stöd för R och python i beräkningen

Nej. R och Python stöds inte i Azure SQL Database.

### <a name="are-the-compute-nodes-containerized"></a>Beräkningsnoderna är behållare

Nej. Databasen finns på en virtuell dator för beräknings- och inte en behållare.

## <a name="performance-questions"></a>Prestanda-frågor

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Hur högt dataflöde kan jag skicka i den största beräkningen i hyperskala för SQL-databas

Vi har sett en konsekvent 100 MB/s av ändringsdata (transaction log data generation)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Hur många IOPS visas i den största beräkningen i hyperskala för SQL-databas

IOPS och i/o-svarstid varierar beroende på arbetsbelastningmönster.  Om data behöver nås är lokala för den beräkning cache, blir den samma i/o-mönster som lokal SSD-lagring.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Mina dataflöde hämta påverkas av säkerhetskopior

Nej. Beräkningen är frikopplad från lagringsskikt att undvika påverkan på beräkning.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Få min dataflödet påverkas som jag lägga till ytterligare beräkningsnoder

Eftersom lagringen delas och det finns ingen direkt fysiska replikering sker mellan primära och sekundära beräkningsnoder, tekniskt sett påverkas dataflödet i primära noden inte genom att lägga till lässkala som noder. Vi kan dock begränsa kontinuerlig aggressiva arbetsbelastning för att tillåta log tillämpas på sekundära noder och sidan servrar komma ikapp och undvika dåliga läsprestanda på sekundära noder.

## <a name="scalability-questions"></a>Frågor om skalbarhet

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Hur lång tid det tar att skala uppåt och nedåt en beräkningsnod

Skala beräkning upp eller ned tar 5 – 10 minuter, oavsett storlek.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Är min databas offline medan att skala upp/ned åtgärden pågår

Nej. Att skala upp och ned kommer att vara online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Kan jag förvänta mig anslutning släpp när skalningsåtgärder pågår

Skala upp eller ned resulterar i befintliga anslutningar som håller på att släppas när redundansväxlingen sker till Beräkningsnoden med målstorleken. Att lägga till skrivskyddade repliker resulterar inte i anslutningen förlorade meddelanden.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Är att skala upp och ned compute-noder som är automatisk eller slutanvändaren utlöste åtgärden

Slutanvändaren. Inte automatiskt.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Har mina `tempb` också utvidga i takt med beräkningen skalas upp

Ja. Temp db skalas upp automatiskt när beräkningen växer.  

### <a name="can-i-provision-multiple-primary-computes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Jag kan lägga till flera primära beräkningar, till exempel ett flera huvudservrar system där flera primära beräkning huvuden kan få en högre grad av samtidighet

Nej. Endast primära Beräkningsnoden accepterar läsningar/skrivningar. Sekundär compute-noder kan bara godkänna skrivskyddade förfrågningar.

## <a name="read-scale-questions"></a>Läs skala frågor

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Hur många sekundära compute-noder kan jag etablera

Vi kan skapa 2 repliker för storskaliga databaser som standard. Om du vill justera antalet repliker, kan du göra det med hjälp av [Azure-portalen](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Hur ansluter jag till de här sekundära compute-noder

Du kan ansluta till dessa extra skrivskyddad beräkningsnoder genom att ange den `ApplicationIntent` argumentet i anslutningssträngen till `read_only`. Alla anslutningar som markerats med `read-only` dirigeras automatiskt till en ytterligare skrivskyddad compute-noder.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Jag kan skapa en särskild slutpunkt för lässkala repliken

Nej. Du kan bara ansluta till lässkala repliken genom att ange `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Gör systemet intelligent belastningsutjämning av arbetsbelastningen för läsning

Nej. Läs endast arbetsbelastningen är till en slumpmässig lässkala replik.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Jag kan skala upp/ned sekundära beräkningsnoderna oberoende av den primära beräkningen

Nej. Sekundär compute-noder används också för hög tillgänglighet, så måste de vara samma konfiguration som primärt vid redundans.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Får jag olika temp db storlek för min primära beräknings- och min ytterligare sekundära compute-noder

Nej. Din `tempdb` konfigureras baserat på beräkning storlek etableringen, sekundära compute-noder har samma storlek som den primära databearbetning.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Kan jag lägga till index och vyer på min sekundära compute-noder

Nej. Hyperskala databaser innehåller delad lagring, vilket innebär att alla compute-noder finns i samma tabeller, index och vyer. Om du vill ha ytterligare index som är optimerade för läsningar på sekundära – du måste lägga till dem på primärt först.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Hur lång fördröjningen det ska vara mellan primära och sekundära compute-nod

Från den tid som en transaktion har genomförts på primärt, beroende på log generation pris, kan det antingen vara ögonblicklig eller låg millisekunder.

## <a name="next-steps"></a>Nästa steg

Läs mer om tjänstnivån hyperskala [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md).
