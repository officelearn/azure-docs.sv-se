---
title: Vanliga frågor och svar om Azure SQL Database storskalig | Microsoft Docs
description: Svar på vanliga frågor som kunder ställer om en Azure SQL-databas i den storskaliga tjänst nivån – vanligt vis kallas för en storskalig databas.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/06/2019
ms.openlocfilehash: 951d5bb10fbeeac090a1edb510b7214855477eac
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515350"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Vanliga frågor och svar om Azure SQL-storskaliga databaser

Den här artikeln innehåller svar på vanliga frågor om kunder som överväger en databas i Azure SQL Database storskalig tjänst nivå, som ofta kallas för en storskalig databas. I den här artikeln beskrivs de scenarier som stöds av storskaliga tjänster och funktionen för kors funktioner är kompatibla med SQL Database storskalig i allmänhet.

- Vanliga frågor och svar är avsedda för läsare som har en kort förståelse för den storskaliga tjänst nivån och som vill ha sina egna frågor och frågor.
- Dessa vanliga frågor och svar är inte avsedda att vara Guidebook eller besvara frågor om hur du använder en SQL Database storskalig databas. För det rekommenderar vi att du läser [Azure SQL Database storskalig](sql-database-service-tier-hyperscale.md) dokumentation.

## <a name="general-questions"></a>Allmänna frågor

### <a name="what-is-a-hyperscale-database"></a>Vad är en storskalig databas?

En storskalig databas är en Azure SQL-databas på den storskaliga tjänst nivå som backas upp av den storskaliga lagrings tekniken. En storskalig databas har stöd för upp till 100 TB data och ger hög genom strömning och prestanda, samt snabb skalning för att anpassa sig till arbets belastnings kraven. Skalning är transparent för program – anslutning, bearbetning av frågor och så vidare, fungerar som alla andra SQL-databaser.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Vilka resurs typer och inköps modeller stöder storskalig skalning

Den storskaliga Service nivån är endast tillgänglig för enskilda databaser med den vCore-baserade inköps modellen i Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Hur skiljer sig den storskaliga tjänst nivån från Generell användning och Affärskritisk tjänst nivåerna

VCore-baserade tjänst nivåer skiljer sig i huvudsak baserat på tillgänglighet, lagrings typ och IOPs.

- Generell användning tjänst nivån är lämplig för de flesta affärs arbets belastningar och erbjuder en balanserade uppsättning beräknings-och lagrings alternativ där IO-fördröjning eller växlings tider inte är prioritet.
- Den storskaliga tjänst nivån är optimerad för mycket stora databas arbets belastningar.
- Affärskritisk tjänst nivån är lämplig för företags arbets belastningar där IO-fördröjningen är en prioritet.

| | Resurstyp | Generellt syfte |  Storskalig | Verksamhetskritisk |
|:---:|:---:|:---:|:---:|:---:|
| **Bäst för** |Alla|  De flesta företags arbets belastningar. Erbjuder budget orienterade balanserade beräknings-och lagrings alternativ. | Data program med krav på stor data kapacitet och möjlighet att skala lagrings utrymme och skala automatiskt. | OLTP-program med hög transaktions hastighet och lägsta latens i/o. Ger högsta möjliga återhämtning till problem med flera isolerade repliker.|
|  **Resurstyp** ||Enkel databas/elastisk pool/hanterad instans | Enskild databas | Enkel databas/elastisk pool/hanterad instans |
| **Beräknings storlek**|Enkel databas/elastisk pool * | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor * | 1 till 80 virtuella kärnor |
| |Hanterad instans | 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Gäller inte | 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| **Lagringstyp** | Alla |Premium-Fjärrlagring (per instans) | Fristående lagring med lokal SSD-cache (per instans) | Super-fast lokal SSD-lagring (per instans) |
| **Lagrings storlek** | Enkel databas/elastisk pool | 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | Hanterad instans  | 32 GB – 8 TB | Gäller inte | 32 GB – 4 TB |
| **IO-dataflöde** | Enkel databas * * | 500 IOPS per vCore med 7000 maximal IOPS | Hög skalning är en arkitektur med flera nivåer med cachelagring på flera nivåer. Effektiv IOPs är beroende av arbets belastningen. | 5000 IOPS med 200 000 högsta IOPS|
| | Hanterad instans | Beror på fil storleken | Gäller inte | Hanterad instans: Beror på fil storleken|
|**Tillgänglighet**|Alla|1 replik, ingen Read-Scale, ingen lokal cache | Flera repliker, upp till 15 Read-Scale, delvis lokalt cacheminne | 3 repliker, 1 Read-Scale, Zone-redundant HA, fullständig lokal cache |
|**Regelbundet**|Alla|RA-GRS, 7-35 dagar (7 dagar som standard)| RA-GRS, 7 dagar, konstant tidpunkts återställning (PITR) | RA-GRS, 7-35 dagar (7 dagar som standard) |

\*Elastiska pooler stöds inte i den storskaliga tjänst nivån

### <a name="who-should-use-the-hyperscale-service-tier"></a>Vem ska använda den storskaliga tjänst nivån

Den storskaliga Service nivån är främst avsedd för kunder som har stora lokala SQL Server databaser och vill modernisera sina program genom att flytta till molnet eller för kunder som redan använder Azure SQL Database och vill utöka risken för databas tillväxt. Hög skalning är också avsett för kunder som söker både höga prestanda och hög skalbarhet. Med storskalighet får du:

- Stöd för upp till 100 TB databas storlek
- Snabba databas säkerhets kopieringar oavsett databas storlek (säkerhets kopior baseras på ögonblicks bilder av filer)
- Snabba databas återställningar oavsett databas storlek (återställningar är från ögonblicks bilder av filer)
- Högre logg data flöde resulterar i snabba transaktions överförings tider oavsett databas storlek
- Läs ut skalbart till en eller flera skrivskyddade noder för att avlasta din Läs arbets belastning och för snabb växling.
- Snabbt skala upp till beräkning, i konstant tid, som är mer kraftfullt för att hantera den stora arbets belastningen och sedan skala ned, i konstant tid. Detta liknar att skala upp och ned mellan en P6 till en p11, till exempel, men mycket snabbare eftersom detta inte är en storlek på data åtgärd.

### <a name="what-regions-currently-support-hyperscale"></a>Vilka regioner som för närvarande stöder storskalighet

Den Azure SQL Database nivån på den storskaliga nivån är för närvarande tillgänglig i de regioner som anges under [Azure SQL Database översikt över flera nivåer](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Kan jag skapa flera storskaliga databaser per logisk server

Ja. Mer information och begränsningar för antalet storskaliga databaser per logisk server finns i [SQL Database resurs begränsningar för databaser med enkel och pool på en logisk server](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Vad är prestanda egenskaperna för en storskalig databas

SQL Database storskaliga arkitektur ger hög prestanda och data flöde och stöder stora databas storlekar. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Vad är skalbarheten för en storskalig databas

SQL Database storskalig ger snabb skalbarhet baserat på din arbets belastnings efter frågan.

- **Skala upp/ned**

  Med storskalig skalning kan du skala upp den primära beräknings storleken i termer av resurser som CPU, minne och sedan skala ned, i konstant tid. Eftersom lagrings utrymmet är delat är skalning och skalning ned inte en storlek på data åtgärd.  
- **Skala in/ut**

  Med storskalighet får du också möjlighet att etablera en eller flera ytterligare Compute-noder som du kan använda för att hantera dina Läs begär Anden. Det innebär att du kan använda dessa ytterligare Compute-noder som skrivskyddade noder för att avlasta din Läs arbets belastning från den primära beräkningen. Förutom skrivskyddade, fungerar dessa noder också som frekvent vänt i händelse av en redundansväxling från den primära.

  Etableringen av varje ytterligare Compute-noder kan göras i konstant tid och är en online-åtgärd. Du kan ansluta till dessa ytterligare skrivskyddade Compute-noder genom att ange `ApplicationIntent` argumentet för anslutnings strängen till. `readonly` Alla anslutningar som marker `readonly` ATS med dirigeras automatiskt till en av de ytterligare skrivskyddade Compute-noderna.

## <a name="deep-dive-questions"></a>Djupgående frågor

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Kan jag blanda storskalig och enstaka databaser på en enda logisk server

Ja, det kan du.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Kräver storskalig min programmerings modell för att ändra

Nej, din programmerings modell finns kvar. Du använder anslutnings strängen som vanligt och andra vanliga lägen för att interagera med din Azure SQL-databas.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Vilka isolerings nivåer för transaktioner som ska användas som standard på SQL Database storskalig databas

På den primära noden, är transaktions isolerings nivån RCSI (Read Committed ögonblicks bild isolering). På de sekundära noderna för Läs skala är isolerings nivån ögonblicks bild.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Kan jag använda min lokala eller IaaS SQL Server licens för att SQL Database storskalig

Ja, [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) är tillgängligt för storskaliga. Varje SQL Server Standard kärna kan mappas till 1 storskalig virtuella kärnor. Varje SQL Server Enterprise kärna kan mappas till 4 virtuella kärnor. Du behöver inte en SQL-licens för sekundära repliker. Azure Hybrid-förmån priset tillämpas automatiskt på Read-Scale (sekundär) repliker.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Vilken typ av arbets belastningar är SQL Database storskalig design för

SQL Database storskalig stöder alla SQL Server arbets belastningar, men den är främst optimerad för OLTP. Du kan också lägga till hybrid-och analys-och data mart-arbetsbelastningar.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Hur kan jag välja mellan Azure SQL Data Warehouse och SQL Database storskalig

Om du kör interaktiva analys frågor med SQL Server som ett informations lager är SQL Database storskaligt ett bra alternativ eftersom du kan vara värd för relativt små informations lager (till exempel några TB upp till tiotal TB) till en lägre kostnad och du kan migrera dina data arehouse-arbetsbelastning till SQL Database storskalig utan T-SQL-kod ändringar.

Om du kör data analys i stor skala med komplexa frågor och använder Parallel Data Warehouse (PDW), Teradata eller andra data lager för massivt parallell processor (MPP), kan SQL Data Warehouse vara det bästa valet.
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database storskaliga beräknings frågor

### <a name="can-i-pause-my-compute-at-any-time"></a>Kan jag pausa min data bearbetning när som helst

Inte just nu, men du kan skala din beräkning och antalet repliker nedåt för att minska kostnaderna under låg belastnings tider.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Kan jag etablera en beräkning med extra RAM-minne för min minnes intensiv arbets belastning

Nej. Om du vill ha mer RAM-minne måste du uppgradera till en högre beräknings storlek. Mer information finns i [storskalig lagring och beräknings storlekar](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Kan jag etablera flera datornoder av olika storlekar

Nej.

### <a name="how-many-read-scale-replicas-are-supported"></a>Hur många Läs skalnings repliker som stöds

De storskaliga databaserna skapas med en Read-Scale-replik (två repliker totalt) som standard. Du kan skala antalet skrivskyddade repliker mellan 0 och 4 med hjälp av [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) eller [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>För hög tillgänglighet måste jag etablera ytterligare Compute-noder

I storskaliga databaser tillhandahålls återhämtning på lagrings nivå. Du behöver bara en replik för att tillhandahålla återhämtning. När beräknings repliken är avstängd skapas en ny replik automatiskt utan data förlust.

Men om det bara finns en replik kan det ta en stund att skapa den lokala cachen i den nya repliken efter redundansväxlingen. Under fasen för cache-återuppbyggnad hämtar databasen data direkt från sidan servrar, vilket resulterar i försämrade IOPS och frågornas prestanda.

För verksamhets kritiska appar som kräver hög tillgänglighet bör du etablera minst 2 Compute-noder inklusive den primära Compute-noden (standard). På så sätt finns det en snabb växling som är tillgänglig i händelse av en redundansväxling.

## <a name="data-size-and-storage-questions"></a>Data storlek och lagrings frågor

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Vad är den maximala DB-storlek som stöds med SQL Database storskalig

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Vad är storleken på transaktions loggen med storskalig

Transaktions loggen med stor skala är nästan oändlig. Du behöver inte bekymra dig om att köra slut på logg utrymme på ett system med hög logg data flöde. Logg skapande takten kan dock begränsas för kontinuerliga aggressiva arbets belastningar. Den högsta varaktiga logg skapande hastigheten är ungefär 100 MB/SEK.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Växer min temporära databas som min databas

`tempdb` Databasen finns på lokal SSD-lagring och konfigureras utifrån den beräknings storlek som du etablerar. Din `tempdb` är optimerad och har getts ut för att ge högsta möjliga prestanda. `tempdb` Storleken kan inte konfigureras och hanteras av del systemet Storage.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Växer storleken på databasen automatiskt eller måste jag hantera storleken på datafilerna

Databas storleken ökar automatiskt när du infogar/matar in mer data.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Vad är den minsta databas storlek som SQL Database storskalig stöder eller börjar med

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>I vilka ökningar växer min databas storlek

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Är lagringen i SQL Database storskalig lokal eller fjärran sluten

I storskalig lagring lagras datafiler i Azure standard Storage. Data cachelagras kraftigt på lokal SSD-lagring på datorer som ligger nära datornoderna. Dessutom har Compute-noderna en cache på lokal SSD och in-memory (buffer, och så vidare) för att minska frekvensen med att hämta data från fjärrnoder.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kan jag hantera eller definiera filer eller fil grupper med storskalig skalning

Nej
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kan jag etablera en hård tak för data tillväxten för min databas

Nej

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Hur är datafiler som är placerade med SQL Database storskalig

Datafilerna styrs av sid servrar. Allteftersom data storleken växer läggs datafiler och associerade Page Server-noder till.

### <a name="is-database-shrink-supported"></a>Stöds databas krympning

Nej

### <a name="is-database-compression-supported"></a>Stöds databas komprimering

Ja

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Om jag har en stor tabell, blir mina tabell data spridda över flera datafiler

Ja. Data sidorna som är kopplade till en specifik tabell kan ingå i flera datafiler, som är en del av samma filgrupp. SQL Server använder en [proportionell fyllnings strategi](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) för att distribuera data över datafiler.

## <a name="data-migration-questions"></a>Frågor om datamigrering

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Kan jag flytta mina befintliga Azure SQL-databaser till den storskaliga tjänst nivån

Ja. Du kan flytta dina befintliga Azure SQL-databaser till storskaliga. Det här är en enkelriktad migrering. Du kan inte flytta databaser från storskalig till en annan tjänst nivå. Vi rekommenderar att du gör en kopia av dina produktions databaser och migrerar till storskaligt för koncept bevis (POC).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Kan jag flytta mina storskaliga databaser till andra versioner

Nej. För närvarande kan du inte flytta en storskalig databas till en annan tjänst nivå.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Förlorar jag alla funktioner eller funktioner efter migreringen till den storskaliga tjänst nivån

Ja. Några av Azure SQL Database funktioner stöds inte i storskaliga ännu, inklusive men inte begränsad långsiktig säkerhets kopiering. När du har migrerat databaserna till storskaligt upphör dessa funktioner att fungera.  Vi räknar med att de här begränsningarna är tillfälliga.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Kan jag flytta min lokala SQL Server-databas eller min SQL Server virtuella dator databas till storskalig

Ja. Du kan använda alla befintliga migrerings tekniker för att migrera till storskalig, inklusive BACPAC, transaktionell replikering, logisk data inläsning. Se även [Azure Database migration service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Vad är drift stopp under migreringen från en lokal miljö eller virtuell dator miljö till skalbarhet och hur kan jag minimera den

Nedtid är detsamma som stillestånds tiden när du migrerar dina databaser till en enda databas i Azure SQL Database. Du kan använda [](replication-to-sql-database.md#data-migration-scenario
) Transaktionsreplikering för att minimera migreringen av drift stopp för databaser upp till några TB i storlek. För mycket stora databaser (10 + TB) kan du överväga att migrera data med hjälp av ADF, Spark eller andra tekniker för data förflyttning.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Hur lång tid tar det att ta upp X data för att SQL Database storskalig

Storskaligheten kan förbruka 100 MB/s nya/ändrade data.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Kan jag läsa data från Blob Storage och göra snabba inläsningar (t. ex. PolyBase och SQL Data Warehouse)

Du kan läsa data från Azure Storage och läsa in data inläsning i en storskalig databas (precis som du kan göra med en vanlig enkel databas). PolyBase stöds för närvarande inte i Azure SQL Database. Du kan göra PolyBase med [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) eller köra ett Spark-jobb i [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) med [Spark-anslutaren för SQL](sql-database-spark-connector.md). Spark-anslutningen till SQL stöder Mass infogning.

Enkel återställning eller Mass loggnings modell stöds inte i storskaliga versioner. Fullständig återställnings modell krävs för att tillhandahålla hög tillgänglighet. Storskaligheten ger dock en bättre data inmatnings hastighet jämfört med en enda Azure SQL-databas på grund av den nya logg arkitekturen.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Tillåter SQL Database storskalig etablering av flera noder för att mata in stora mängder data

Nej. SQL Database storskaliga är en SMP-arkitektur och är inte en asymmetrisk multiprocessering eller en arkitektur med flera huvud servrar. Du kan bara skapa flera repliker för att skala ut skrivskyddade arbets belastningar.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Vad är den äldsta SQL Server versionen SQL Database stöd för storskalig migrering från

SQL Server 2005. Mer information finns i [migrera till en enskild databas eller en databas i poolen](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). För kompatibilitetsproblem, se [lösa kompatibilitetsproblem med databas migrering](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Stöder SQL Database storskalig migrering från andra data källor som Aurora, MySQL, Oracle, DB2 och andra databas plattformar

Ja. Det kommer från andra data källor än SQL Server kräver logisk migrering. Du kan använda [Azure Database migration service](../dms/dms-overview.md) för en logisk migrering.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Frågor om verksamhets kontinuitet och haveri beredskap

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Vilka SLA-avtal tillhandahålls för en storskalig databas

Med standard primärt plus 1 läsbart sekundärt, är service avtalet 99,95% tillgänglighet.  Med fler repliker går SLA upp till 99,99%.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Är de databas säkerhets kopior som hanteras av tjänsten Azure SQL Database

Ja

### <a name="how-often-are-the-database-backups-taken"></a>Hur ofta säkerhets kopiering av databasen görs

Det finns inga traditionella fullständiga, differentiella och logg säkerhets kopior för SQL Database storskaliga databaser. I stället finns det vanliga ögonblicks bilder av datafilerna och loggen som genereras är helt enkelt bevarad så länge den kvarhållna perioden har kon figurer ATS eller är tillgänglig för dig.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>SQL Database skalnings plats för stöd punkt i Time Restore

Ja

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Vad är återställnings punkt målet för återställnings punkt mål (RTO) med säkerhets kopiering/återställning i SQL Database storskalig

Återställningen är 0 min. RTO-målet är mindre än 10 minuter, oavsett databasens storlek. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Påverkar säkerhets kopiering av stora databaser beräknings prestanda på min primära

Nej. Säkerhets kopior hanteras av underlag rings systemet och utnyttjar fil ögonblicks bilder. De påverkar inte användar arbets belastningen på den primära.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Kan jag utföra geo-återställning med en SQL Database storskalig databas

Ja.  Geo-återställning stöds fullt ut.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Kan jag konfigurera geo-replikering med SQL Database storskalig databas

Inte just nu.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Får jag geo-replikerade mina sekundära Compute-noder med SQL Database storskalig

Inte just nu.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Kan jag göra en SQL Database säkerhets kopia av en databas och återställa den till min lokala server eller SQL Server i virtuell dator

Nej. Lagrings formatet för storskaliga databaser skiljer sig från traditionella SQL Server och du kan inte kontrol lera säkerhets kopior eller ha åtkomst till dem. Om du vill ta dina data från en SQL Database storskalig databas använder du antingen export tjänsten eller använder skript plus BCP.

## <a name="cross-feature-questions"></a>Kors funktions frågor

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Förlorar jag alla funktioner eller funktioner efter migreringen till den storskaliga tjänst nivån

Ja. Några av Azure SQL Database funktioner stöds inte i storskalig, inklusive men inte begränsad långsiktig säkerhets kopiering. När du har migrerat databaserna till storskaligt upphör dessa funktioner att fungera.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>PolyBase fungerar med SQL Database storskalig

Nej. PolyBase stöds inte på Azure SQL Database.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Har beräkningen stöd för R och python

Nej. R och python stöds inte i Azure SQL Database.

### <a name="are-the-compute-nodes-containerized"></a>Är Compute-noderna behållare

Nej. Databasen finns på en beräknings-VM och inte en behållare.

## <a name="performance-questions"></a>Prestanda frågor

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Hur mycket data flöde kan jag dra på den största SQL Database storskalig data bearbetning

Vi har sett en konsekvent 100 MB/SEK av ändrings data (generering av transaktions logg data)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Hur många IOPS får jag på den största SQL Database storskalig data bearbetning

IOPS-och IO-svars tiden varierar beroende på arbets belastnings mönstren.  Om de data som behöver nås är lokala i Compute-cachen kommer det att vara samma IO-mönster som lokal SSD.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Påverkas mitt data flöde av säkerhets kopior

Nej. Compute är frikopplad från lagrings lagret för att undvika påverkan på beräkning.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Påverkas mitt data flöde när jag etablerar ytterligare Compute-noder

Eftersom lagrings utrymmet delas och det inte sker någon direkt fysisk replikering mellan primära och sekundära datornoder, kommer data flödet på den primära noden inte att påverkas om du lägger till storskaliga noder. Vi kan dock begränsa den kontinuerliga aggressiva arbets belastningen till att tillåta loggen att gälla för sekundära noder och sid servrar för att komma igång och undvika dåliga Läs prestanda på sekundära noder.

## <a name="scalability-questions"></a>Skalbarhets frågor

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Hur lång tid tar det att skala upp och ned en Compute-nod

Skalnings beräkningar bör ta 5-10 minuter oavsett data storlek.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Är min databas offline medan åtgärden för att skala upp/ned pågår

Nej. Uppskalning uppåt och nedåt är online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Ska jag förvänta dig att släppa anslutningen när skalnings åtgärderna pågår

Att skala upp eller ned resulterar i att befintliga anslutningar bryts när redundansväxlingen sker till Compute-noden med mål storleken. Att lägga till Läs repliker resulterar inte i anslutning.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Är skala upp och ned i datornoderna automatisk eller slutanvändare utlöst åtgärd

Slutanvändare. Inte automatisk.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>`tempb` Växer även i takt med att beräkningen skalas upp

Ja. Temp DB skalas upp automatiskt när beräkningen växer.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Kan jag etablera flera primära Compute-noder, till exempel ett system med flera huvud servrar där flera primära beräknings huvuden kan driva en högre nivå av samtidighet

Nej. Endast den primära Compute-noden accepterar begär Anden om läsning och skrivning. Sekundära datornoder accepterar endast skrivskyddade begär Anden.

## <a name="read-scale-questions"></a>Läs skalnings frågor

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Hur många sekundära Compute-noder kan jag etablera

Vi skapar två repliker för storskaliga databaser som standard. Om du vill justera antalet repliker kan du göra det med hjälp av [Azure Portal](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Hur gör jag för att ansluta till dessa sekundära Compute-noder

Du kan ansluta till dessa ytterligare skrivskyddade Compute-noder genom att ange `ApplicationIntent` argumentet för anslutnings strängen till. `readonly` Alla anslutningar som marker `readonly` ATS med dirigeras automatiskt till en av de ytterligare skrivskyddade Compute-noderna.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Kan jag skapa en dedikerad slut punkt för den expanderade repliken

Nej. Du kan bara ansluta till Read-Scale Replica genom att `ApplicationIntent=ReadOnly`ange.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Utför systemet intelligent belastnings utjämning för Läs arbets belastningen

Nej. Den skrivskyddade arbets belastningen omdirigeras till en slumpmässig storskalig replik.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Kan jag skala upp/ned de sekundära Compute-noderna oberoende av den primära beräkningen

Nej. De sekundära datornoderna används också för HA, så de måste vara samma konfiguration som den primära, i händelse av en redundansväxling.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Får jag en annan temp DB-storlek för min primära beräkning och mina ytterligare sekundära Compute-noder

Nej. Din `tempdb` konfigureras baserat på beräknings storlekens etablering, de sekundära datornoderna har samma storlek som den primära beräkningen.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Kan jag lägga till index och vyer på mina sekundära Compute-noder

Nej. Storskaliga databaser har delad lagring, vilket innebär att alla Compute-noder ser samma tabeller, index och vyer. Om du vill att ytterligare index är optimerade för läsningar på sekundär – måste du lägga till dem på den primära först.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Hur lång fördröjning kommer att vara mellan den primära och sekundära Compute-noden

Från den tidpunkt då en transaktion allokeras på primärt, beroende på loggens generations frekvens, kan det antingen skrivas över eller i låg millisekunder.

## <a name="next-steps"></a>Nästa steg

Mer information om den storskaliga tjänst nivån finns i [storskalig Service Tier](sql-database-service-tier-hyperscale.md).
