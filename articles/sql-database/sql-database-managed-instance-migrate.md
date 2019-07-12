---
title: Migrera databas från SQL Server-instans till Azure SQL Database - hanterad instans | Microsoft Docs
description: Lär dig hur du migrerar en databas från SQL Server-instans till Azure SQL Database - hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 11/07/2019
ms.openlocfilehash: 7cf54b79fac87905117e321574571890c59315e6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827068"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrering av SQL Server-instans till Azure SQL Database-hanterad instans

I den här artikeln får du lära dig om metoder för att migrera en SQL Server 2005 eller senare version instans till [Azure SQL Database-hanterad instans](sql-database-managed-instance.md). Information om hur du migrerar till en enkel databas eller elastisk pool finns i [migrera till en enda eller grupperade databas](sql-database-cloud-migrate.md). Migreringsinformation om hur du migrerar från andra plattformar finns i [Databasmigreringsguide för Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Om du vill att snabbt starta och prova Managed Instance, kanske du vill gå till [snabbstartsguiden](/sql-database-managed-instance-quickstart-guide.md) i stället för den här sidan. 

Migreringen av databasen ut som på en hög nivå:

![Migreringsprocessen](./media/sql-database-managed-instance-migration/migration-process.png)

- [Utvärdera hanterad instans kompatibilitet](#assess-managed-instance-compatibility) där bör du kontrollera att det inte finns några blockeringsproblem som kan förhindra att din migreringar.
  - Det här steget omfattar också skapandet av [baslinje för prestanda](#create-performance-baseline) att fastställa resursanvändningen på din SQL Server-instans. Det här steget krävs om du vill att o distribuera korrekt storlekar Managed Instance och kontrollera att inte påverkas prestanda efter migreringen.
- [Välj app-anslutningsalternativ](sql-database-managed-instance-connect-app.md)
- [Distribuera till ett optimalt storlekar managed instance](#deploy-to-an-optimally-sized-managed-instance) där du ska välja tekniska egenskaper (antal virtuella kärnor, minnesmängd) och prestandanivå (affärskritisk, generell användning) för din hanterade instans.
- [Välj migreringsmetod och migrera](#select-migration-method-and-migrate) där du migrera dina databaser med hjälp av offline-migrering (interna säkerhetskopiering/återställning, databasen importe/export) eller onlinemigrering (Data Migration Service, Transaktionsreplikering).
- [Övervaka program](#monitor-applications) så att du har förväntade prestanda.

> [!NOTE]
> Om du vill migrera en enskild databas i en enkel databas eller elastisk pool, se [migrera en SQL Server-databas till Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Utvärdera kompatibilitet för hanterad instans

Börja med att kontrollera om hanterad instans är kompatibel med databaskrav av ditt program. Alternativ för hanterad instans-distribution är utformad att ge enkel lift and shift-migrering för flesta av befintliga program som använder SQL Server lokalt eller på virtuella datorer. Men du kan ibland behöva funktioner eller funktioner som ännu inte stöds och kostnaden för att implementera en lösning är för höga.

Använd [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) för att identifiera potentiella kompatibilitetsproblem påverkar databasfunktionaliteten på Azure SQL Database. DMA inte ännu stöd för hanterad instans som mål för migrering, men det rekommenderas att köra utvärderingen mot Azure SQL Database och noga igenom listan över rapporterade funktionsparitet och kompatibilitetsproblem mot produktdokumentationen. Se [Azure SQL Database-funktioner](sql-database-features.md) att kontrollera finns det några rapporterat allvarliga problem som inte blockeringar i hanterad instans, eftersom de flesta av blockeringsproblem som hindrar en migrering till Azure SQL Database har tagits bort med hanteras instans. För instans, funktioner som databasöverskridande frågor, transaktioner mellan databaser inom samma instans, länkad server för att andra SQL källor, CLR, globala temporära tabeller är instans på vyer, Service Broker och liknande tillgängliga i hanterade instanser.

Om det finns några rapporterade blockeringsproblem som inte tas bort med alternativ för hanterad instans-distribution kan du behöva tänka på ett alternativ som [SQL Server på Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). Här följer några exempel:

- Om du kräver direkt åtkomst till operativsystemet eller filsystem, till exempel att installera från tredje part eller anpassade agenter på samma virtuella dator med SQL Server.
- Om du har strikta beroende på funktioner som fortfarande inte stöds, till exempel FileStream / filetable-objekt, PolyBase och transaktioner över flera instanser.
- Om absolut behöver att hålla sig i en specifik version av SQL Server (2012, till exempel).
- Om dina beräkningskrav är mycket lägre kan den hantera instansen (1 vCore, till exempel) och konsolidering av databasen inte är acceptabel.

Om du har löst alla identifierade migreringsblockerare och du fortsätter migreringen till hanterad instans, Observera att vissa ändringar kan påverka prestanda för din arbetsbelastning:
- Obligatorisk fullständiga återställningsmodellen och regelbundna schema för automatisk säkerhetskopiering kan påverka prestandan för din arbetsbelastning eller underhåll/ETL-åtgärder om du har med jämna mellanrum används för enkel-/ massloggade modellen eller stoppats säkerhetskopieringar på begäran.
- Annan server eller databas på konfigurationer, till exempel spårningsflaggor eller kompatibilitetsnivå
- Nya funktioner som du använder till exempel Transparent databasen datakryptering (TDE) eller automatisk redundans grupper kan påverka processor-och IO.

Hanterade instans vi garanterar 99,99% tillgänglighet även i de viktiga scenarierna, så overhead på grund av dessa funktioner inte kan inaktiveras. Mer information finns i [rotorsaker som kan orsaka olika prestanda på SQL Server och Managed Instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Skapa baslinje för prestanda

Om du vill jämföra prestanda för din arbetsbelastning på hanterad instans med din ursprungliga arbetsbelastningar som körs på SQL Server skulle du behöva skapa en baslinje för prestanda som ska användas för jämförelse. 

Baslinje för prestanda är en uppsättning parametrar som medelvärde/max CPU-användning, genomsnitt/max disk-i/o-svarstid, dataflöde, IOPS, varaktighet för sida genomsnittet/max, genomsnittlig maxstorleken på tempdb. Du skulle vilja ha liknande eller ännu bättre parametrar efter migreringen, så det är viktigt att mäta och registrera baslinje-värden för dessa parametrar. Förutom systemparametrar, skulle du behöva välja ett representativt frågorna eller de viktigaste frågorna i din arbetsbelastning och mått min/genomsnittlig/max varaktighet, CPU-användningen för de valda förfrågningarna. Dessa värden gör att du kan jämföra prestanda för arbetsbelastningar som körs på Managed Instance till de ursprungliga värdena på din SQL Server-källans.

Några av de parametrar som du kommer att mäta på SQL Server-instansen är: 
- [Övervaka CPU-användningen på SQL Server-instansen](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) och registrera genomsnittliga och högsta CPU-användning.
- [Övervaka minnesanvändning på SQL Server-instansen](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) och avgör hur mycket minne som används av olika komponenter, till exempel buffertpoolen, planera cache, columnstore poolen, [minnesintern OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)osv. Dessutom bör du hitta genomsnittliga och högsta värden för varaktighet för sida prestandaräknaren för minne.
- Övervaka disk-i/o-användning på källan SQL Server instans med [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) vy eller [prestandaräknare](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Övervaka arbetsbelastning och frågeprestanda eller SQL Server-instansen genom att undersöka Dynamic Management Views eller Query Store om du migrerar från SQL Server 2016 + version. Identifiera Genomsnittlig varaktighet och CPU-användningen för de viktigaste frågorna i arbetsbelastningen och jämföra dem med de frågor som körs på den hanterade instansen.

> [!Note]
> Om du upptäcker några problem med din arbetsbelastning på SQL Server som hög CPU-användning, konstant minnesbelastning, tempdb-eller parametrization bör du försöka att lösa dem på din SQL Server-instans innan du tar baslinje- och migrering. Migrera vet att alla nya system migh orsaka oväntade resultat och ogiltigförklaras alla prestanda jämförelse.

Som ett resultat av den här aktiviteten bör du ha dokumenterade medelvärde och högsta värden för processor, minne och i/o-användning på ditt källsystem samt genomsnittlig och max varaktighet och CPU-användningen för den dominerande ställning och de viktigaste frågorna i din arbetsbelastning. Du bör använda dessa värden senare för att jämföra prestanda för din arbetsbelastning på hanterad instans med baslinjeprestanda för arbetsbelastningen för SQL Server-källans.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Distribuera till en optimal storlek hanterad instans

Hanterad instans skräddarsys för lokala arbetsbelastningar som planerar att flytta till molnet. Det inför en [nya inköpsmodell](sql-database-service-tiers-vcore.md) som ger större flexibilitet att välja rätt nivå av resurser för dina arbetsbelastningar. I den lokala miljön är du antagligen van vid att ändra storlek på dessa arbetsbelastningar med hjälp av fysiska kärnor och i/o-bandbredd. Inköpsmodellen för den hanterade instansen baseras på virtuella kärnor, eller ”vCores”, med ytterligare lagringsutrymme och I/O som är tillgängliga separat. VCore-modellen är ett enklare sätt att förstå dina beräkningskrav i molnet eller det du använder en lokal idag. Den nya modellen kan du justera storleken din mål-miljö i molnet. Här beskrivs några allmänna riktlinjer som kan hjälpa dig att välja rätt tjänstnivå och egenskaper:
- Baserat på baslinjen CPU-användning kan du etablera en hanterad instans som matchar antalet kärnor som du använder på SQL Server, att ha i åtanke att processoregenskaper behöva skalas för att matcha [Virtuella datoregenskaper där Managed Instance är installerade](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Baserat på minnesanvändningen baslinje väljer [tjänstnivån med matchande minne](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). Mängden minne kan inte väljas direkt så att du måste välja den hanterade instansen med antal virtuella kärnor med matchande minne (till exempel 5.1 GB/vCore i Gen5). 
- Baserat på baslinjen i/o svarstiden för undersystemet filen välja mellan generell användning (svarstiden är större än 5 MS) och affärskritisk tjänstnivåer (svarstid mindre än 3 ms).
- Baserat på baslinjen dataflöde förväg allokera storleken på data eller loggfiler för att hämta förväntat i/o-prestanda.

Du kan välja beräknings- och lagringsresurser vid distributionen tid och ändra den senare utan att driftstopp för dina program med hjälp av den [Azure-portalen](sql-database-scale-resources.md):

![hanterad instans-storlek](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Läs hur du skapar infrastrukturen som virtuellt nätverk och en hanterad instans i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Det är viktigt att hålla dina mål VNet och undernät alltid enligt [hanterad instans VNet-krav](sql-database-managed-instance-connectivity-architecture.md#network-requirements). All inkompatibilitet kan hindra dig från att skapa nya instanser eller använda dem som du redan har skapat. Läs mer om [skapar en ny](sql-database-managed-instance-create-vnet-subnet.md) och [konfigurera befintliga](sql-database-managed-instance-configure-vnet-subnet.md) nätverk.

## <a name="select-migration-method-and-migrate"></a>Välj migreringsmetod och migrera

De hanterade instansen distribution alternativet mål användarscenarier som kräver drivrutiner för masslagring Databasmigrering från lokala eller IaaS databas implementeringar. De är optimala val när du vill flytta över serverdelen för de program som regelbundet använder instansnivå och / eller databasöverskridande funktioner. Om det här är ditt scenario kan du flytta en hel instans till en motsvarande miljö i Azure utan att behöva göra om arkitekturen i dina program.

Om du vill flytta SQL-instanser måste du planera noggrant:

- Migrering av alla databaser som måste vara samordnad (som körs på samma instans)
- Migrering av på instansnivå objekt som ditt program är beroende av, inklusive inloggningar, autentiseringsuppgifter, SQL-Agentjobb och operatorer och på serverutlösare.

Managed instance är en hanterad tjänst som gör att du kan delegera vissa av de ordinarie DBA-åtgärder för plattformen som de är inbyggda i. Därför vissa nivå instansdata inte behöver migreras, till exempel Underhållsjobb för regelbundna säkerhetskopieringar eller Always On-konfiguration som [hög tillgänglighet](sql-database-high-availability.md) är inbyggd i.

Hanterad instans stöder följande databas-migreringsalternativ (för närvarande dessa är de enda migreringsstorleken metoderna):

- Azure Database Migration Service - migrering med nästan obefintlig nedtid
- Interna `RESTORE DATABASE FROM URL` – använder interna säkerhetskopieringar från SQL Server och kräver vissa avbrott.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

Den [Azure Database Migration Service (DMS)](../dms/dms-overview.md) är en fullständigt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid. Den här tjänsten effektiviserar uppgifter som krävs för att flytta befintliga från tredje part och SQL Server-databaser till Azure. Distributionsalternativ i den offentliga förhandsversionen inkludera databaser i Azure SQL Database och SQL Server-databaser i en Azure virtuell dator. DMS är den rekommenderade metoden för migrering för dina företagsarbetsbelastningar.

Om du använder SQL Server Integration Services (SSIS) i SQL Server lokalt, DMS stöder ännu inte migrera SSIS-katalogen (SSISDB) som lagrar SSIS-paket, men du kan etablera Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) som kommer Skapa en ny SSISDB i en hanterad instans och sedan kan du distribuera om dina paket, se [skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Läs mer om det här scenariot och konfigurationssteg för att DMS i [migrera din lokala databas till managed instance med DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Intern återställning från URL

ÅTERSTÄLLNING av interna säkerhetskopieringar (.bak-filer) som kommer från SQL Server lokalt eller [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/)är tillgängligt på [Azure Storage](https://azure.microsoft.com/services/storage/), är en av de viktigaste funktionerna för hanterad instans-distribution alternativ som gör att snabbt och enkelt offlinedatabasen.

Följande diagram ger en översikt över processen:

![migrering flöde](./media/sql-database-managed-instance-migration/migration-flow.png)

Följande tabell innehåller mer information om de metoder som du kan använda beroende på källan SQL Server-version du kör:

|Steg|SQL-motor och version|Säkerhetskopiera / återställa metoden|
|---|---|---|
|Placera säkerhetskopiering till Azure Storage|Tidigare SQL 2012 SP1 CU2|Ladda upp bak-filen direkt till Azure storage|
||2012 SP1 CU2 - 2016|Direkt säkerhetskopiering med inaktuella [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntax|
||2016 och senare|Direkt säkerhetskopiering med [med SAS-AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Återställa från Azure storage till hanterad instans|[ÅTERSTÄLLA från URL: en med SAS-AUTENTISERINGSUPPGIFTER](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - När du migrerar en databas som skyddas av [Transparent datakryptering](transparent-data-encryption-azure-sql.md) till en hanterad instans med alternativ för inbyggd återställning, motsvarande certifikat från en lokal eller IaaS SQL Server måste migreras innan databasen återställa. Detaljerade anvisningar finns i [migrera TDE-certifikat till managed instance](sql-database-managed-instance-migrate-tde-certificate.md)
> - Återställning av systemdatabaser stöds inte. Om du vill migrera nivå instansobjekt (lagras i master- eller msdb-databaser), rekommenderar vi att skriva ut dem och köra T-SQL-skript på mål-instans.

En Snabbstart som visar hur du återställer en säkerhetskopia av databasen till en hanterad instans med hjälp av SAS-autentiseringsuppgifter, se [Återställ från säkerhetskopia till en hanterad instans](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Övervakning av program

När du har slutfört migreringen till Managed Instance kan spåra du programmets beteende och prestanda för din arbetsbelastning. Den här processen omfattar följande aktiviteter:
- [Jämför resultat av arbetsbelastning som körs på den hanterade instansen](#compare-performance-with-the-baseline) med den [baslinje för prestanda som du skapade på SQL Server-källans](#create-performance-baseline).
- Kontinuerligt [övervaka prestandan för din arbetsbelastning](#monitor-performance) att identifiera potentiella problem och förbättring.

### <a name="compare-performance-with-the-baseline"></a>Jämföra prestanda med baslinjen

Den första aktiviteten som du skulle behöva vidta omedelbart efter att migreringen är att jämföra prestanda för arbetsbelastningen med standardprestanda för arbetsbelastning. Målet med den här aktiviteten är att bekräfta att arbetsbelastningsprestandan på din hanterade instans uppfyller dina behov. 

Databasmigrering till Managed Instance ser för databasen och dess ursprungliga kompatibilitetsnivå i de flesta fall. De ursprungliga inställningarna bevaras när det är möjligt för att minska risken för vissa prestandaförsämringar jämfört med källan SQL Server. Om en användardatabas kompatibilitetsnivå 100 eller högre före migreringen, förblir densamma efter migreringen. Om en användardatabas kompatibilitetsnivå 90 före migreringen i den uppgraderade databasen anges kompatibilitetsnivå till 100, vilket är lägsta stöds kompatibilitetsnivån i hanterad instans. Kompatibilitetsnivån för systemdatabaser är 140. Eftersom migrering till Managed Instance faktiskt migrerar till den senaste versionen av SQL Server Database Engine, bör du vara medveten om att du behöver testa prestanda så att vissa överraskande prestandaproblem undviks.

En förutsättning är att se till att du har slutfört följande aktiviteter:
- Justera dina inställningar på hanterad instans med inställningarna från SQL Server-källinstansen genom att undersöka olika instans, databas, temdb inställningar och konfigurationer. Kontrollera att du inte har ändrat inställningarna som kompatibilitetsnivå eller kryptering innan du kör den första prestanda jämförelsen eller godta risken att några av de nya funktionerna som du har aktiverat kan påverka vissa frågor. Ändra kompatibilitetsnivån för databas för att minska migreringsrisker förrän prestandaövervakning.
- Implementera [riktlinjer för lagring för bästa praxis för generell användning](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) , till exempel förväg allokera storleken på filerna som ska få bättre prestanda.
- Lär dig mer om den [viktiga skillnader i miljön som kan orsaka skillnader i prestanda mellan Managed Instance och SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) och identifiera risker som kan påverka prestanda.
- Se till att du behåller den aktiverad Query Store och automatisk justering på din hanterade instans. Dessa funktioner kan du mäta arbetsbelastningen prestanda och automatiskt åtgärda potentiella prestandaproblem. Lär dig att använda Query Store som en optimal verktyg för att hämta information om arbetsbelastningens prestanda före och efter databas nivåändring för kompatibilitet, enligt beskrivningen i [hålla prestanda stabilitet under uppgraderingen till nyare version av SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
När du har förberett den miljö som är jämförbar så mycket som möjligt med din lokala miljö, du kan börja köra din arbetsbelastning och mäta prestanda. Processen för mätning bör innehålla samma parametrar som du mäts [medan du skapar baslinjeprestanda för din arbetsbelastning mått på SQL Server-källans](#create-performance-baseline).
Därför bör du jämför prestandaparametrar med baslinjen och identifiera viktiga skillnader.

> [!NOTE]
> I många fall skulle du inte att kunna få exakt matchande prestanda på hanterad instans och SQL Server. Hanterad instans är en SQL Server-databasmotorn men infrastruktur och konfiguration med hög tillgänglighet på hanterad instans kan införa vissa skillnaden. Du kan förvänta dig att vissa frågor skulle vara snabbare medan andra kan vara långsammare. Målet med jämförelse är att verifiera att arbetsbelastningens prestanda i hanterade instanser matchar prestanda på SQL Server (i genomsnitt) och identifiera finns det några viktiga frågor med prestanda som inte matchar din ursprungliga prestanda.

Resultatet av jämförelsen prestanda kan vara:
- Högsta prestanda på hanterad instans har justerats eller bättre som arbetsbelastningsprestandan på SQL Server. I det här fallet har du har bekräftat att migreringen har lyckats.
- Merparten av prestandaparametrar och frågorna i arbetsbelastningen arbetet själv, med vissa undantag med försämrad prestanda. I det här fallet skulle du behöva identifiera skillnaderna och deras prioritet. Om det finns några viktiga frågor med försämrad prestanda, bör du undersöka de underliggande SQL-planerna ändras eller frågorna stöter på några resursbegränsningar. Minskning kan i det här fallet vara att använda några tips på viktiga frågor (till exempel ändrade kompatibilitetsnivå kan äldre kardinalitetsberäkningen) antingen direkt eller med hjälp av planguider, återskapa eller skapa statistik och index som kan påverka prenumerationerna. 
- De flesta av frågorna som är långsammare på hanterad instans jämfört med källan SQL Server. I det här fallet försöker identifiera rotorsaken till skillnaden som [når vissa resursgräns]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits) som i/o-gränser, minnesgräns, instans log hastighetsbegränsning osv. Om det finns ingen gräns för resursen som kan orsaka skillnaden, försöker ändra kompatibilitetsnivån för databasen eller ändra inställningar för databasen som äldre kardinalitetsberäknare och starta testet. Granska rekommendationerna tillhandahålls av Managed Instance eller Query Store vyer för att identifiera de frågor som försämrat prestanda.

> [!IMPORTANT]
> Hanterad instans har inbyggda plan för automatisk korrigering funktion som är aktiverad som standard. Den här funktionen ser till att frågor som fungerade bra i Klistra in inte skulle försämras i framtiden. Se till att den här funktionen är aktiverad och att du har genomfört arbetsbelastning tillräckligt länge med de gamla inställningarna innan du ändrar nya inställningar för att aktivera hanterad instans för mer information om baslinjeprestanda och planer.

Gör ändringar av parametrar eller uppgradera tjänstnivåer att Konvergera till den bästa konfigurationen tills du kommer arbetsbelastningsprestandan som passar dina behov.

### <a name="monitor-performance"></a>Övervaka prestanda

Hanterad instans erbjuder en mängd olika avancerade verktyg för övervakning och felsökning och du bör använda dem för att övervaka prestanda på-instansen. Vissa parametrar som din behöver övervaka är:
- CPU-användning på instansen som ska bestämma gör att antalet virtuella kärnor som du etablerade är rätt matchning för din arbetsbelastning.
- Sidan vida på din hanterade instans att fastställa [behöver du ytterligare minne](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Vänta statistik som `INSTANCE_LOG_GOVERNOR` eller `PAGEIOLATCH` som talar om har du problem med lagring i/o, särskilt på allmän-nivån där du kan behöva förväg allokera filer för att få bättre i/o-prestanda.

## <a name="leverage-advanced-paas-features"></a>Använd avancerade PaaS-funktioner

När du är på en helt hanterad plattform och du har kontrollerat att arbetsbelastningen prestanda kommer att matcha du prestanda för SQL Server-arbetsbelastning kan du ta fördelar som tillhandahålls automatiskt som en del av tjänsten SQL Database. 

Även om du inte göra några ändringar i hanterade instansen under migreringen, är det hög risken som du vill aktivera på några av de nya funktionerna när klustret fungerar din instans om du vill utnyttja de senaste förbättringarna i database engine. Vissa ändringar är endast aktiverad när den [kompatibilitetsnivån för databas har ändrats](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Exempelvis kan du behöver inte skapa säkerhetskopior på hanterad instans - tjänsten utför säkerhetskopieringar åt dig automatiskt. Du måste inte längre tänka på schemaläggning, tar och hantera säkerhetskopior. Hanterad instans ger dig möjlighet att återställa till valfri punkt inom denna kvarhållning period med [peka i tiden Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Dessutom kan du inte behöver bekymra dig om hur du konfigurerar hög tillgänglighet som [hög tillgänglighet](sql-database-high-availability.md) är inbyggd i.

Överväg att använda för att stärka säkerheten, [Azure Active Directory Authentication](sql-database-security-overview.md), [granskning](sql-database-managed-instance-auditing.md), [hotidentifiering](sql-database-advanced-data-security.md), [försäkerhetpåradnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), och [dynamisk datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Förutom avancerade hanterings- och säkerhetsfunktioner Managed Instance tillhandahåller en uppsättning avancerade verktyg som kan hjälpa dig att [övervaka och finjustera din arbetsbelastning](sql-database-monitor-tune-overview.md). [Azure SQL-analys](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) kan du övervaka ett stort antal instanser som hanteras och centralisera övervakning av ett stort antal instanser och databaser. [Automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) i Managed Instance kontinuerligt övervaka prestanda SQL planen körning statistik och automatiskt åtgärda identifierade prestandaproblem.

## <a name="next-steps"></a>Nästa steg

- Information om hanterade instanser finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
- Se en självstudie som innehåller en återställning från en säkerhetskopia, [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- Självstudie som visar migrering med DMS, se [migrera din lokala databas till managed instance med DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
