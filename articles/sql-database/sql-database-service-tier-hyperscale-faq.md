---
title: Vanliga frågor och svar om azure SQL Database hyperskala
description: Svar på vanliga frågor som kunder ställer om en Azure SQL-databas på tjänstnivån Hyperskala – vanligen kallad hyperskaladatabas.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268631"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Vanliga frågor och svar om azure SQL Database hyperskala

Den här artikeln innehåller svar på vanliga frågor för kunder som överväger en databas på Azure SQL Database Hyperscale-tjänstnivån, så kallade bara hyperskala i resten av den här vanliga frågor och svar. I den här artikeln beskrivs de scenarier som Hyperscale stöder och de funktioner som är kompatibla med hyperskala.

- Denna FAQ är avsedd för läsare som har en kort förståelse för hyperskala tjänst nivå och funderar på att få sina specifika frågor och funderingar besvarade.
- Den här vanliga frågor och svar är inte avsedd att vara en guidebok eller svara på frågor om hur du använder en hyperskala databas. En introduktion till hyperskala rekommenderar vi att du läser Azure [SQL Database Hyperscale-dokumentationen.](sql-database-service-tier-hyperscale.md)

## <a name="general-questions"></a>Allmänna frågor

### <a name="what-is-a-hyperscale-database"></a>Vad är en hyperskaladatabas

En hyperskaladatabas är en Azure SQL-databas på tjänstnivån Hyperskala som backas upp av lagringstekniken för skalskalning av hyperskala. En hyperskala databas stöder upp till 100 TB data och ger hög dataflöde och prestanda, samt snabb skalning för att anpassa sig till arbetsbelastningskraven. Skalning är transparent för programmet – anslutning, frågebearbetning osv.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Vilka resurstyper och inköpsmodeller har stöd för Hyperscale

Tjänstnivån Hyperskala är endast tillgänglig för enskilda databaser med den vCore-baserade inköpsmodellen i Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Hur skiljer sig tjänstnivån hyperskala från tjänstnivåerna Allmänt ändamål och Affärskritiskt

De vCore-baserade tjänstnivåerna är differentierade baserat på databasens tillgänglighet och lagringstyp, prestanda och maximal storlek, enligt beskrivningen i följande tabell.

| | Resurstyp | Generell användning |  Hyperskala | Affärskritiskt |
|:---:|:---:|:---:|:---:|:---:|
| **Bäst för** |Alla|Erbjuder budgetorienterade balanserade beräknings- och lagringsalternativ.|De flesta arbetsbelastningar för företag. Automatisk skalning av lagringsstorlek på upp till 100 TB, snabb vertikal och horisontell beräkningsskalning, snabb databasåterställning.|OLTP-program med hög transaktionshastighet och låg IO-svarstid. Erbjuder högsta motståndskraft mot fel och snabba redundans med flera synkront uppdaterade repliker.|
|  **Resurstyp** ||Enkel databas / elastisk pool / hanterad instans | Enskild databas | Enkel databas / elastisk pool / hanterad instans |
| **Beräkna storlek**|Enkel databas / elastisk pool * | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor* | 1 till 80 virtuella kärnor |
| |Hanterad instans | 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Ej tillämpligt | 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| **Lagringstyp** | Alla |Premium fjärrlagring (per instans) | Avkopplad lagring med lokal SSD-cache (per instans) | Supersnabb lokal SSD-lagring (per instans) |
| **Lagringsstorlek** | Enkel databas / elastisk pool *| 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | Hanterad instans  | 32 GB – 8 TB | Ej tillämpligt | 32 GB – 4 TB |
| **IOPS** | Enskild databas | 500 IOPS per vCore med 7000 högsta IOPS | Hyperskala är en flerskiktad arkitektur med cachelagring på flera nivåer. Effektiva IOPS beror på arbetsbelastningen. | 5000 IOPS med 200 000 max IOPS|
| | Hanterad instans | Beror på filstorleken | Ej tillämpligt | 1375 IOPS/vCore |
|**Tillgänglighet**|Alla|1 replik, ingen lässkalning, ingen lokal cache | Flera repliker, upp till 4 Lässkalning, partiell lokal cache | 3 repliker, 1 Lässkalning, zonuppsagt HA, full lokal lagring |
|**Säkerhetskopior**|Alla|RA-GRS, 7-35 dagars kvarhållning (7 dagar som standard)| RA-GRS, 7 dagars retention, konstant tidsåtgångsåterhämtning (PITR) | RA-GRS, 7-35 dagars kvarhållning (7 dagar som standard) |

\*Elastiska pooler stöds inte på tjänstnivån Hyperskala

### <a name="who-should-use-the-hyperscale-service-tier"></a>Vem ska använda tjänstnivån Hyperskala

Tjänstnivån Hyperskala är avsedd för kunder som har stora lokala SQL Server-databaser och vill modernisera sina program genom att flytta till molnet eller för kunder som redan använder Azure SQL Database och vill expandera potential för databastillväxt. Hyperskala är också avsedd för kunder som söker både hög prestanda och hög skalbarhet. Med Hyperscale får du:

- Databasstorlek upp till 100 TB
- Snabba säkerhetskopieringar av databaser oavsett databasstorlek (säkerhetskopior baseras på ögonblicksbilder av lagring)
- Snabb återställning av databasen oavsett databasstorlek (återställningar kommer från ögonblicksbilder av lagring)
- Högre loggdataflöde oavsett databasstorlek och antalet virtuella kärnor
- Läs Utskalning med en eller flera skrivskyddade repliker, som används för avlastning av läsning och som aktiva standbys.
- Snabb skala upp av beräkning, i konstant tid, för att vara mer kraftfull för att rymma den tunga arbetsbelastningen och sedan skala ner, i konstant tid. Detta liknar skala upp och ner mellan en P6 och en P11, till exempel, men mycket snabbare eftersom detta inte är en storlek på dataåtgärd.

### <a name="what-regions-currently-support-hyperscale"></a>Vilka regioner har för närvarande stöd för hyperskala

Tjänstnivån Hyperskala är för närvarande tillgänglig i de regioner som anges under [Azure SQL Database Hyperscale Overview](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Kan jag skapa flera hyperskaledatabaser per logisk server

Ja. Mer information och begränsningar om antalet hyperskala databaser per logisk server finns i [SQL Database-resursgränser för enstaka och poolade databaser på en logisk server](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Vilka är prestandaegenskaperna för en hyperskaladatabas

Hyperskala arkitektur ger hög prestanda och dataflöde samtidigt som det stöder stora databasstorlekar. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Vad är skalbarheten för en hyperskaladatabas

Hyperskala ger snabb skalbarhet baserat på din arbetsbelastningsbehov.

- **Skala upp/ned**

  Med Hyperscale kan du skala upp den primära beräkningsstorleken när det gäller resurser som CPU och minne och sedan skala ned, i konstant tid. Eftersom lagringen delas är uppskalning och nedskalning inte en storlek på dataåtgärden.  
- **Skala in/ut**

  Med Hyperscale får du också möjlighet att etablera en eller flera ytterligare beräkningsrepliker som du kan använda för att hantera dina läsbegäranden. Det innebär att du kan använda dessa ytterligare beräkningsrepliker som skrivskyddade repliker för att avlasta din läsarbetsbelastning från den primära beräkningen. Förutom skrivskyddad fungerar dessa repliker även som hot-standbys vid en redundans från den primära.

  Etablering av var och en av dessa ytterligare beräkningsrepliker kan göras i konstant tid och är en onlineåtgärd. Du kan ansluta till dessa ytterligare skrivskyddade `ApplicationIntent` beräkningsrepliker `ReadOnly`genom att ange argumentet på anslutningssträngen till . Alla anslutningar med `ReadOnly` programavsikten dirigeras automatiskt till en av de ytterligare skrivskyddade beräkningsrepliker.

## <a name="deep-dive-questions"></a>Djupdykning frågor

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Kan jag blanda hyperskala och enskilda databaser i en enda logisk server

Ja, det kan du.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Kräver Hyperscale att programmeringsmodellen för mitt program ändras

Nej, programprogrammeringsmodellen förblir som den är. Du använder anslutningssträngen som vanligt och de andra vanliga sätten att interagera med hyperskaladatabasen.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Vilken transaktionsisoleringsnivå är standard i en hyperskaladatabas

På den primära repliken är standardnivån för transaktionsisolering RCSI (Läs bekräftad ögonblicksbildisolering). På sekundära återutstna sekundära repliker för lässkala är standardisoleringsnivån Ögonblicksbild.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Kan jag ta med mig min lokala eller IaaS SQL Server-licens till Hyperscale

Ja, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) är tillgänglig för hyperskala. Varje SQL Server Standard kärna kan mappa till 1 Hyperscale virtuella kärnor. Varje SQL Server Enterprise kärna kan mappa till 4 Hyperscale virtuella kärnor. Du behöver inte en SQL-licens för sekundära repliker. Azure Hybrid-förmånspriset tillämpas automatiskt på lässkalningsrepliker (sekundära).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Vilken typ av arbetsbelastningar är Hyperscale avsedd för

Hyperscale stöder alla SQL Server-arbetsbelastningar, men den är främst optimerad för OLTP. Du kan också ta med arbetsbelastningar för Hybrid (HTAP) och Analytisk (data mart).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Hur kan jag välja mellan Azure SQL Data Warehouse och Azure SQL Database Hyperscale

Om du för närvarande kör interaktiva analysfrågor med SQL Server som informationslager är Hyperscale ett bra alternativ eftersom du kan vara värd för små och medelstora informationslager (till exempel några TB upp till 100 TB) till en lägre kostnad och du kan migrera dina SQL Server-data lagerarbetsbelastningar till hyperskala med minimala T-SQL-kodändringar.

Om du kör dataanalys i stor skala med komplexa frågor och ihållande inmatningshastigheter som är högre än 100 MB/s, eller använder PDW(Parallel Data Warehouse), Teradata eller andra MPP-datalager (Massively Parallel Processing), kan SQL Data Warehouse vara det bästa valet.
  
## <a name="hyperscale-compute-questions"></a>Beräkningsfrågor för hyperskala

### <a name="can-i-pause-my-compute-at-any-time"></a>Kan jag pausa min beräkning när som helst

Inte just nu, men du kan skala din beräkning och antalet repliker ner för att minska kostnaderna under icke-rusningstid.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Kan jag etablera en beräkningsreplik med extra RAM-minne för min minnesintensiva arbetsbelastning

Nej. För att få mer RAM-minne måste du uppgradera till en högre beräkningsstorlek. Mer information finns i [Hyperskala lagring och beräkningsstorlekar](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Kan jag etablera flera beräkningsrepliker av olika storlekar

Nej.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Hur många lässkalningsrepliker stöds

Hyperskaladatabaserna skapas med en lässkalningsreplik (två repliker inklusive primära) som standard. Du kan skala antalet skrivskyddade repliker mellan 0 och 4 med Hjälp av [Azure Portal](https://portal.azure.com) eller [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>För hög tillgänglighet, måste jag etablera ytterligare beräkningsrepliker

I hyperskala databaser tillhandahålls dataåtersåterhet på lagringsnivå. Du behöver bara en replik för att ge återhämtning. När beräkningsrepliken är nere skapas en ny replik automatiskt utan dataförlust.

Men om det bara finns en replik kan det ta lite tid att skapa den lokala cachen i den nya repliken efter redundans. Under cache återskapa fasen hämtar databasen data direkt från sidan servrar, vilket resulterar i högre lagring svarstid och försämrad frågeprestanda.

För verksamhetskritiska appar som kräver hög tillgänglighet med minimal redundanspåverkan bör du etablera minst 2 beräkningsrepliker, inklusive den primära beräkningsrepliken. Det här är standardkonfigurationen. På så sätt finns det en hot-standby replik tillgänglig som fungerar som en redundans mål.

## <a name="data-size-and-storage-questions"></a>Frågor om datastorlek och lagring

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Vilken är den maximala databasstorleken som stöds med Hyperscale

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Vad är storleken på transaktionsloggen med Hyperscale

Transaktionsloggen med Hyperskala är praktiskt taget oändlig. Du behöver inte oroa dig för att få på loggutrymme på ett system som har ett högt loggdataflöde. Logggenereringshastigheten kan dock begränsas för kontinuerlig skrivning av arbetsbelastningar. Den högsta ihållande logggenereringshastigheten är 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Har `tempdb` min skala när min databas växer

Databasen `tempdb` finns på lokal SSD-lagring och är dimensionerad proportionellt mot den beräkningsstorlek som du etablerar. Din `tempdb` är optimerad för att ge maximal prestandafördelar. `tempdb`storlek är inte konfigurerbar och hanteras för dig.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Har min databasstorlek automatiskt växa, eller måste jag hantera storleken på datafiler

Databasstorleken växer automatiskt när du infogar/intar mer data.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Vilken är den minsta databasstorleken som Hyperscale stöder eller börjar med

40 GB. En hyperskaladatabas skapas med startstorleken 10 GB. Sedan börjar den växa med 10 GB var 10:e minut, tills den når storleken 40 GB. Var och en av dessa 10 GB chuckar fördelas i en annan sida server för att ge mer IOPS och högre I / O-parallellism. På grund av den här optimeringen, även om du väljer den ursprungliga databasstorleken mindre än 40 GB, kommer databasen att växa till minst 40 GB automatiskt.

### <a name="in-what-increments-does-my-database-size-grow"></a>I vilka steg växer databasstorleken

Varje datafil växer med 10 GB. Flera datafiler kan växa samtidigt.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Är lagringen i Hyperscale lokal eller fjärransluten

I Hyperscale lagras datafiler i Azure-standardlagring. Data cachelagras helt på lokal SSD-lagring på sidservrar som ligger nära beräkningsrepliker. Dessutom har beräkningsrepliker datacachen på lokal SSD och i minnet för att minska frekvensen för att hämta data från fjärrsidesservrar.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kan jag hantera eller definiera filer eller filgrupper med Hyperskala

Nej. Datafiler läggs till automatiskt. De vanligaste orsakerna till att skapa ytterligare filgrupper gäller inte i arkitekturen Hyperskala lagring.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kan jag etablera ett hårt tak för datatillväxten för min databas

Nej.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Hur är datafiler som anges med Hyperscale

Datafilerna styrs av sidservrar, med en sidserver per datafil. När datastorleken ökar läggs datafiler och tillhörande sidservrar till.

### <a name="is-database-shrink-supported"></a>Stöds databasrr för databas

Nej.

### <a name="is-data-compression-supported"></a>Stöds datakomprimering

Ja, inklusive komprimering av rad, sida och kolumnaffär.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Om jag har en stor tabell, gör mina tabelldata sprids ut över flera datafiler

Ja. De datasidor som är associerade med en viss tabell kan hamna i flera datafiler, som alla ingår i samma filgrupp. SQL Server använder [proportionell fyllningsstrategi](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) för att distribuera data över datafiler.

## <a name="data-migration-questions"></a>Frågor om datamigrering

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Kan jag flytta mina befintliga Azure SQL-databaser till tjänstnivån Hyperskala

Ja. Du kan flytta dina befintliga Azure SQL-databaser till Hyperscale. Detta är en enkelriktad migrering. Du kan inte flytta databaser från hyperskala till en annan tjänstnivå. För proofs of concept (POCs) rekommenderar vi att du gör en kopia av databasen och migrerar kopian till Hyperskala.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Kan jag flytta mina hyperskaledatabaser till andra tjänstnivåer

Nej. För närvarande kan du inte flytta en hyperskala databas till en annan tjänstnivå.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Förlorar jag några funktioner eller funktioner efter migreringen till tjänstnivån Hyperskala

Ja. Vissa azure SQL-databasfunktioner stöds inte i Hyperskala ännu, inklusive men inte begränsat till långsiktig lagring av säkerhetskopiering. När du har migrerat databaserna till Hyperskala slutar dessa funktioner att fungera.  Vi förväntar oss att dessa begränsningar är tillfälliga.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Kan jag flytta min lokala SQL Server-databas eller min SQL Server-databas i en virtuell molndator till Hyperscale

Ja. Du kan använda alla befintliga migreringstekniker för att migrera till Hyperskala, inklusive transaktionsreplikering och andra dataförflyttningstekniker (Masskopia, Azure Data Factory, Azure Databricks, SSIS). Se även [Azure Database Migration Service](../dms/dms-overview.md), som stöder många migreringsscenarier.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Vad är min driftstopp under migreringen från en lokal eller virtuell datormiljö till Hyperskala, och hur kan jag minimera den

Driftstopp för migrering till hyperskala är samma som driftstopp när du migrerar dina databaser till andra Azure SQL Database-tjänstnivåer. Du kan använda [transaktionsreplikering](replication-to-sql-database.md#data-migration-scenario
) för att minimera driftstoppsmigrering för databaser med upp till få TB i storlek. För mycket stora databaser (10+ TB) kan du överväga att migrera data med hjälp av ADF, Spark eller andra datarörelsetekniker.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Hur lång tid skulle det ta att ta in X mängd data till hyperskala

Hyperskala kan förbruka 100 MB/s nya/ändrade data, men den tid som krävs för att flytta data till Azure SQL-databaser påverkas också av tillgängligt nätverksdataflöde, källavläsningshastighet och måldatabastjänstnivåmålet.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Kan jag läsa data från blob storage och göra snabb belastning (som Polybase i SQL Data Warehouse)

Du kan låta ett klientprogram läsa data från Azure Storage och läsa in data i en hyperskaladatabas (precis som du kan med alla andra Azure SQL-databaser). Polybase stöds för närvarande inte i Azure SQL Database. Som ett alternativ för att ge snabb belastning kan du använda [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)eller använda ett Spark-jobb i Azure [Databricks](https://docs.microsoft.com/azure/azure-databricks/) med [Spark-kopplingen för SQL](sql-database-spark-connector.md). Spark-kopplingen till SQL stöder massinfogning.

Det är också möjligt att massavlästa läsa data från Azure Blob-arkivet med BULK INSERT eller OPENROWSET: [Exempel på massåtkomst till data i Azure Blob Storage](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Enkel återställnings- eller massloggningsmodell stöds inte i Hyperskala. Fullständig återställningsmodell krävs för att ge hög tillgänglighet och point-in-time-återställning. Hyperskalaloggarkitektur ger dock bättre datainmatningshastighet jämfört med andra Azure SQL Database-tjänstnivåer.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Tillåter Hyperscale etablering av flera noder för parallell intag av stora mängder data

Nej. Hyperskala är en symmetrisk SMP-arkitektur (Multi-Processing) och är inte en massivt parallell bearbetning (MPP) eller en arkitektur med flera huvud. Du kan bara skapa flera repliker för att skala ut skrivskyddade arbetsbelastningar.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Vilken äldsta SQL Server-version som stöds för migrering till Hyperskala

SQL Server 2005. Mer information finns i [Migrera till en enskild databas eller en poolad databas](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Kompatibilitetsproblem finns i [Lösa kompatibilitetsproblem för databasmigrering](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Har Hyperscale stöd migrering från andra datakällor som Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2, och andra databasplattformar

Ja. [Azure Database Migration Service](../dms/dms-overview.md) stöder många migreringsscenarier.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Frågor om kontinuitet och katastrofåterställning

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Vilka SLA tillhandahålls för en hyperskala databas

Se [SLA för Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Ytterligare sekundära beräkningsrepliker ökar tillgängligheten, upp till 99,99 % för en databas med två eller flera sekundära beräkningsrepliker.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Hanteras databassäkerhetskopiorna åt mig av Azure SQL Database-tjänsten

Ja.

### <a name="how-often-are-the-database-backups-taken"></a>Hur ofta tas säkerhetskopieringen av databasen

Det finns inga traditionella fullständiga, differentiella och logga säkerhetskopior för hyperskala databaser. I stället finns det regelbundna ögonblicksbilder av lagring av datafiler. Logg som genereras behålls helt enkelt som den är för den konfigurerade kvarhållningsperioden, vilket gör att återställning till någon tidpunkt inom kvarhållningsperioden.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Återställer hyperskala tidsåterställning

Ja.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Vad är mål för återställningspunkt /mål för återställningstid (RTO) för återställning av databaser i hyperskala

RPO är 0 min. RTO-målet är mindre än 10 minuter, oavsett databasstorlek. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Påverkar säkerhetskopiering av databaser beräkningsprestanda på mina primära eller sekundära repliker

Nej. Säkerhetskopior hanteras av lagringsundersystemet och utnyttjar ögonblicksbilder av lagring. De påverkar inte användararbetsbelastningar.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Kan jag utföra geo-återställning med en hyperskala databas

Ja. Geoåterställning stöds fullt ut. Till skillnad från point-in-time-återställning kräver geoåterställning en datastorlek. Datafiler kopieras parallellt, så varaktigheten av den här åtgärden beror främst på storleken på den största filen i databasen, snarare än på den totala databasstorleken. Geoåterställningstiden blir betydligt kortare om databasen återställs i Azure-regionen som är [parkopplad](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) med källdatabasens region.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Kan jag ställa in geo-replikering med hyperskala databas

Inte just nu.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Kan jag ta en hyperskala databas backup och återställa den till min lokala server, eller på SQL Server i en virtuell dator

Nej. Lagringsformatet för hyperskaledatabaser skiljer sig från alla utgivna versioner av SQL Server och du styr inte säkerhetskopior eller har åtkomst till dem. Om du vill ta bort dina data från en hyperskaladatabas kan du extrahera data med hjälp av dataförflyttningstekniker, d.v.s. Azure Data Factory, Azure Databricks, SSIS osv.

## <a name="cross-feature-questions"></a>Frågor om flera funktioner

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Förlorar jag några funktioner eller funktioner efter migreringen till tjänstnivån Hyperskala

Ja. Vissa azure SQL-databasfunktioner stöds inte i Hyperskala, inklusive men inte begränsat till långsiktig lagring av säkerhetskopiering. När du har migrerat databaserna till Hyperskala slutar dessa funktioner att fungera.

### <a name="will-polybase-work-with-hyperscale"></a>Kommer Polybase arbeta med hyperskala

Nej. Polybase stöds inte i Azure SQL Database.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Har Hyperscale stöd för R och Python

Inte just nu.

### <a name="are-compute-nodes-containerized"></a>Är beräkningsnoder behållare

Nej. Hyperskalningsprocesser körs på en [Service Fabric-noder](https://azure.microsoft.com/services/service-fabric/) (VMs), inte i behållare.

## <a name="performance-questions"></a>Prestandafrågor

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Hur mycket skrivdataflöde kan jag driva i en hyperskala databas

Transaktionslogggenomströmningstaket är inställt på 100 MB/s för alla beräkningsstorlekar för hyperskala. Möjligheten att uppnå den här hastigheten beror på flera faktorer, inklusive men inte begränsat till arbetsbelastningstyp, klientkonfiguration och tillräcklig beräkningskapacitet på den primära beräkningsrepliken för att producera logg med den här hastigheten.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Hur många IOPS får jag på den största beräkningen

IOPS och IO-svarstid varierar beroende på arbetsbelastningsmönstren. Om de data som används cachelagras på beräkningsrepliken visas liknande I/O-prestanda som med lokal SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Påverkas mitt dataflöde av säkerhetskopior

Nej. Beräkning är frikopplad från lagringslagret. Detta eliminerar prestandapåverkan av säkerhetskopiering.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Påverkas mitt dataflöde när jag etablerar ytterligare beräkningsrepliker

Eftersom lagringen delas och det inte sker någon direkt fysisk replikering mellan primära och sekundära beräkningsrepliker, påverkas dataflödet på den primära repliken inte direkt genom att lägga till sekundära repliker. Vi kan dock begränsa kontinuerlig aggressivt skriva arbetsbelastning på den primära så att loggen tillämpas på sekundära repliker och sidservrar för att komma ikapp, för att undvika dålig läsprestanda på sekundära repliker.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Hur diagnostiserar och felsöker jag prestandaproblem i en hyperskaladatabas

För de flesta prestandaproblem, särskilt de som inte är rotade i lagringsprestanda, gäller vanliga SQL Server-diagnostik- och felsökningssteg. För Hyperskalaspecifik lagringsdiagnostik finns i [FELSÖKNING AV SQL Hyperscale-prestandafelsökningsdiagnostik](sql-database-hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Frågor om skalbarhet

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Hur lång tid tar det att skala upp och ned en beräkningsreplik

Skala beräkning upp eller ned bör ta 5-10 minuter oavsett datastorlek.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Är min databas offline medan upp/ned-åtgärden för upp- och nedstugga pågår

Nej. Skalningen upp och ner kommer att vara online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Ska jag förvänta mig anslutningsfall när skalningsåtgärderna pågår

Skala upp eller ned resulterar i att befintliga anslutningar tas bort när en redundans sker i slutet av skalningsåtgärden. Att lägga till sekundära repliker resulterar inte i anslutningsdroppar.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Är skalning upp och ned av beräkningsrepliker automatisk eller slutanvändaren utlöst åtgärd

Slutanvändaren. Inte automatiskt.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Har storleken på `tempdb` min databas växer också som beräkningen skalas upp

Ja. Databasen `tempdb` skalas upp automatiskt när beräkningen växer.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Kan jag etablera flera primära beräkningsrepliker, till exempel ett flerhuvudsystem, där flera primära beräkningshuvuden kan driva en högre nivå av samtidighet

Nej. Endast den primära beräkningsrepliken accepterar läs-/skrivbegäranden. Sekundära beräkningsrepliker accepterar bara skrivskyddade begäranden.

## <a name="read-scale-out-questions"></a>Läs utskalningsfrågor

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Hur många sekundära beräkningsrepliker kan jag etablera

Vi skapar en sekundär replik för hyperskala databaser som standard. Om du vill justera antalet repliker kan du göra det med hjälp av [Azure Portal](https://portal.azure.com) eller [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Hur ansluter jag till dessa sekundära beräkningsrepliker

Du kan ansluta till dessa ytterligare skrivskyddade `ApplicationIntent` beräkningsrepliker `ReadOnly`genom att ange argumentet på anslutningssträngen till . Alla anslutningar som `ReadOnly` är markerade med dirigeras automatiskt till en av de ytterligare skrivskyddade beräkningsrepliker.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Hur validerar jag om jag har anslutit till sekundär beräkningsreplik med SSMS eller andra klientverktyg?

Du kan köra följande T-SQL-fråga: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
Resultatet är `READ_ONLY` om du är ansluten till en `READ_WRITE` skrivskyddad sekundär replik och om du är ansluten till den primära repliken. Observera att databaskontexten måste anges till namnet på hyperskaledatabasen, inte till `master` databasen.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Kan jag skapa en dedikerad slutpunkt för en lässkalningsreplik

Nej. Du kan bara ansluta till lässkalningsrepliker genom att `ApplicationIntent=ReadOnly`ange .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Gör systemet intelligent belastningsutjämning av läsarbetsbelastningen

Nej. En ny anslutning med skrivskyddad avsikt omdirigeras till en godtycklig lässkalningsreplik.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Kan jag skala upp/ned de sekundära beräkningsrepliker oberoende av den primära repliken

Nej. Den sekundära beräkningsrepliken används också som redundansmål med hög tillgänglighet, så de måste ha samma konfiguration som den primära för att ge förväntad prestanda efter redundans.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Får jag `tempdb` olika storlek för min primära beräkning och mina ytterligare sekundära beräkningsrepliker

Nej. Databasen `tempdb` är konfigurerad baserat på etableringen av beräkningsstorlek, dina sekundära beräkningsrepliker har samma storlek som den primära beräkningen.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Kan jag lägga till index och vyer på mina sekundära beräkningsrepliker

Nej. Hyperskala databaser har delad lagring, vilket innebär att alla beräkningsrepliker ser samma tabeller, index och vyer. Om du vill ha ytterligare index optimerade för läsningar på sekundärt måste du lägga till dem på den primära.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Hur stor fördröjning kommer det att finnas mellan de primära och sekundära beräkningsrepliker

Datafördröjning från det att en transaktion har bekräftats på primärt till den tid den visas på en sekundär beror på aktuell logggenereringshastighet. Typisk datafördröjning är i låga millisekunder.

## <a name="next-steps"></a>Efterföljande moment

Mer information om tjänstnivån Hyperskala finns i [Hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md).
