---
title: Migrera från SQL Server till hanterad instans
description: Lär dig hur du migrerar en databas från SQL Server-instans till Azure SQL Database - Hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208932"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server-instansmigrering till Azure SQL Database hanterad instans

I den här artikeln får du lära dig mer om metoderna för att migrera en SQL Server 2005- eller senare versionsinstans till [Azure SQL Database-hanterad instans](sql-database-managed-instance.md). Information om hur du migrerar till en enskild databas eller elastisk pool finns i [Migrera till en enda eller poolad databas](sql-database-cloud-migrate.md). Information om migrering om migrering från andra plattformar finns i [Azure Database Migration Guide](https://datamigration.microsoft.com/).

> [!NOTE]
> Om du snabbt vill starta och prova Hanterad instans kanske du vill gå till [snabbstartsguiden](sql-database-managed-instance-quickstart-guide.md) i stället för den här sidan. 

På en hög nivå ser databasmigreringsprocessen ut:

![migreringsprocessen](./media/sql-database-managed-instance-migration/migration-process.png)

- [Utvärdera hanterad instanskompatibilitet](#assess-managed-instance-compatibility) där du bör se till att det inte finns några blockeringsproblem som kan förhindra migreringar.
  - Det här steget innehåller även skapande av [prestandabaslinje](#create-performance-baseline) för att fastställa resursanvändning på din SQL Server-instans för källan. Det här steget behövs om du vill o distribuera korrekt storlek hanterad instans och kontrollera att prestanda efter migreringen inte påverkas.
- [Välj alternativ för appanslutning](sql-database-managed-instance-connect-app.md)
- [Distribuera till en hanterad instans](#deploy-to-an-optimally-sized-managed-instance) av optimal storlek där du väljer tekniska egenskaper (antal virtuella kärnor, mängden minne) och prestandanivå (affärskritiskt, allmänt syfte) för din hanterade instans.
- [Välj migreringsmetod och migrera](#select-migration-method-and-migrate) var du migrerar databaserna med offlinemigrering (inbyggd säkerhetskopiering/återställning, databasimport/export) eller onlinemigrering (Datamigreringstjänst, transaktionsreplikering).
- [Övervaka program](#monitor-applications) för att säkerställa att du har förväntat prestanda.

> [!NOTE]
> Information om hur du migrerar en enskild databas till antingen en enskild databas eller en elastisk pool finns i [Migrera en SQL Server-databas till Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Utvärdera kompatibilitet med hanterade instanser

Ta först reda på om hanterad instans är kompatibel med databaskraven för ditt program. Distributionsalternativet för hanterade instanser är utformat för att ge enkel lyft- och skiftmigrering för de flesta befintliga program som använder SQL Server lokalt eller på virtuella datorer. Du kan dock ibland kräva funktioner som ännu inte stöds och kostnaden för att implementera en lösning är för hög.

Använd [DMA (Data Migration Assistant)](https://docs.microsoft.com/sql/dma/dma-overview) för att identifiera potentiella kompatibilitetsproblem som påverkar databasfunktionerna i Azure SQL Database. DMA har ännu inte stöd för hanterad instans som migreringsmål, men det rekommenderas att köra utvärdering mot Azure SQL Database och noggrant granska listan över rapporterade funktionsparitets- och kompatibilitetsproblem mot produktdokumentation. Se [Azure SQL Database-funktioner](sql-database-features.md) för att kontrollera finns det några rapporterade blockeringsproblem som inte blockerar i hanterad instans, eftersom de flesta av blockeringsproblemen som förhindrar en migrering till Azure SQL Database har tagits bort med hanterad instans. Till exempel funktioner som korsdatabasfrågor, transaktioner över flera databaser inom samma instans, länkad server till andra SQL-källor, CLR, globala temp-tabeller, instansnivåvyer, Service Broker och liknande är tillgängliga i hanterade instanser.

Om det finns några rapporterade blockeringsproblem som inte tas bort med distributionsalternativet för hanterade instanser kan du behöva överväga ett alternativt alternativ, till exempel [SQL Server på virtuella Azure-datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/). Här följer några exempel:

- Om du behöver direkt åtkomst till operativsystemet eller filsystemet, till exempel för att installera tredje part eller anpassade agenter på samma virtuella dator med SQL Server.
- Om du har strikt beroende av funktioner som fortfarande inte stöds, till exempel FileStream / FileTable, PolyBase och korsinstanstransaktioner.
- Om absolut behöver stanna på en viss version av SQL Server (2012, till exempel).
- Om dina beräkningskrav är mycket lägre än hanterad instans erbjudanden (en vCore, till exempel) och databaskonsolidering är inte acceptabelt alternativ.

Om du har löst alla identifierade migreringsblockerare och fortsätter migreringen till Hanterad instans bör du tänka på att vissa av ändringarna kan påverka arbetsbelastningens prestanda:
- Obligatorisk fullständig återställningsmodell och regelbundet schema för automatisk säkerhetskopiering kan påverka prestanda för dina arbetsbelastnings- eller underhålls-/ETL-åtgärder om du regelbundet har använt enkel/massloggad modell eller stoppat säkerhetskopior på begäran.
- Olika konfigurationer på server- eller databasnivå, till exempel spårningsflaggor eller kompatibilitetsnivåer
- Nya funktioner som du använder, till exempel TDE (Transparent Database Encryption) eller grupper för automatisk redundans, kan påverka CPU- och IO-användningen.

Hanterad instans garanterar 99,99 % tillgänglighet även i de kritiska scenarierna, så omkostnader som orsakas av dessa funktioner kan inte inaktiveras. Mer information finns [i de bakomliggande orsakerna som kan orsaka olika prestanda på SQL Server och Hanterad instans](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Skapa prestandabaslinje

Om du behöver jämföra prestanda för din arbetsbelastning på hanterad instans med den ursprungliga arbetsbelastningen som körs på SQL Server, måste du skapa en prestandabaslinje som ska användas för jämförelse. 

Prestandabaslinje är en uppsättning parametrar som genomsnittlig/max CPU-användning, genomsnittlig/max disk-IO-svarstid, dataflöde, IOPS, genomsnittlig/max sidlivslängd, genomsnittlig maxstorlek för tempdb. Du vill ha liknande eller ännu bättre parametrar efter migreringen, så det är viktigt att mäta och registrera baslinjevärdena för dessa parametrar. Förutom systemparametrar måste du välja en uppsättning representativa frågor eller de viktigaste frågorna i din arbetsbelastning och mäta min/medel/max-varaktighet, CPU-användning för de valda frågorna. Dessa värden gör att du kan jämföra prestanda för arbetsbelastning som körs på hanterad instans med de ursprungliga värdena på källan SQL Server.

Några av de parametrar som du skulle behöva mäta på DIN SQL Server-instans är: 
- [Övervaka CPU-användningen på DIN SQL Server-instans](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) och registrera den genomsnittliga och högsta CPU-användningen.
- [Övervaka minnesanvändningen på SQL Server-instansen](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) och bestämma hur mycket minne som används av olika komponenter, till exempel buffertpool, plancache, kolumnlagringspool, [OLTP i minnet](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)osv. Dessutom bör du hitta genomsnittliga och högsta värden för prestandaräknare för sidlivsförväntningsminne.
- Övervaka disk-I/O-användning på källans SQL Server-instans med [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) vy eller [prestandaräknare](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Övervaka arbetsbelastnings- och frågeprestanda eller sql server-instansen genom att undersöka dynamiska hanteringsvyer eller Query Store om du migrerar från SQL Server 2016+-versionen. Identifiera genomsnittlig varaktighet och CPU-användning av de viktigaste frågorna i din arbetsbelastning för att jämföra dem med de frågor som körs på den hanterade instansen.

> [!Note]
> Om du märker något problem med din arbetsbelastning på SQL Server, till exempel hög CPU-användning, konstant minnestryck, tempdb- eller parameteriseringsproblem, bör du försöka lösa dem på din SQL Server-instans innan du tar baslinjen och migreringen. Migrera vet problem till alla nya system migh orsaka oväntade resultat och ogiltigförklara alla prestandajämförelser.

Som ett resultat av den här aktiviteten bör du ha dokumenterade genomsnittliga och högsta värden för CPU- och minnes- och IO-användning på källsystemet, samt genomsnittlig och max varaktighet och CPU-användning av de dominerande och mest kritiska frågorna i din arbetsbelastning. Du bör använda dessa värden senare för att jämföra prestanda för din arbetsbelastning på hanterad instans med baslinjeprestanda för arbetsbelastningen på källan SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Distribuera till en hanterad instans av optimal storlek

Hanterad instans är skräddarsydd för lokala arbetsbelastningar som planerar att flytta till molnet. Det introducerar en [ny inköpsmodell](sql-database-service-tiers-vcore.md) som ger större flexibilitet när du väljer rätt resursnivå för dina arbetsbelastningar. I den lokala världen är du förmodligen van vid att dimensionera dessa arbetsbelastningar med hjälp av fysiska kärnor och IO-bandbredd. Inköpsmodellen för hanterad instans baseras på virtuella kärnor, eller "vCores", med ytterligare lagring och IO tillgängligt separat. VCore-modellen är ett enklare sätt att förstå dina beräkningskrav i molnet jämfört med vad du använder lokalt idag. Med den här nya modellen kan du rätt storlek på din målmiljö i molnet. Några allmänna riktlinjer som kan hjälpa dig att välja rätt tjänstnivå och egenskaper beskrivs här:
- Baserat på baslinjen CPU-användning kan du etablera en hanterad instans som matchar antalet kärnor som du använder på SQL Server, med tanke på att CPU-egenskaper kan behöva skalas för att matcha [VM-egenskaper där hanterad instans är installerad](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Baserat på baslinjen minnesanvändning välja [den tjänstnivå som har matchande minne](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). Mängden minne kan inte väljas direkt så du måste välja den hanterade instansen med mängden virtuella kärnor som har matchande minne (till exempel 5,1 GB/vCore i Gen5). 
- Baserat på baslinje-IO-svarstiden för filundersystemet väljer mellan allmänt syfte (svarstid större än 5 ms) och affärskritiska tjänstnivåer (svarstid mindre än 3 ms).
- Baserat på baslinjedataflöde förallokerar storleken på data eller loggfiler för att få förväntad IO-prestanda.

Du kan välja beräknings- och lagringsresurser vid distributionen och sedan ändra dem efteråt utan att införa driftstopp för ditt program med [Azure-portalen:](sql-database-scale-resources.md)

![hanterad instansstorlek](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Mer information om hur du skapar VNet-infrastrukturen och en hanterad instans finns i [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Det är viktigt att hålla din destination VNet och undernätet alltid i enlighet med [hanterade instans VNet-krav](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Eventuell inkompatibilitet kan hindra dig från att skapa nya instanser eller använda de som du redan har skapat. Läs mer om [att skapa nya](sql-database-managed-instance-create-vnet-subnet.md) och konfigurera [befintliga](sql-database-managed-instance-configure-vnet-subnet.md) nätverk.

## <a name="select-migration-method-and-migrate"></a>Välj migreringsmetod och migrera

Alternativet för distribution av hanterade instanser är inriktat på användarscenarier som kräver massdatabasmigrering från lokala implementeringar eller implementeringar av IaaS-databas. De är optimala val när du behöver lyfta och flytta den bakre änden av de program som regelbundet använder instansnivå och / eller cross-database funktioner. Om det här är ditt scenario kan du flytta en hel instans till en motsvarande miljö i Azure utan att behöva arkitekt dina program.

Om du vill flytta SQL-instanser måste du planera noggrant:

- Migreringen av alla databaser som behöver samlokalisering (sådana som körs på samma instans)
- Migreringen av instansobjekt som ditt program är beroende av, inklusive inloggningar, autentiseringsuppgifter, SQL-agentjobb och operatörer samt utlösare på servernivå.

Hanterad instans är en hanterad tjänst som gör att du kan delegera några av de vanliga DBA-aktiviteterna till plattformen när de är inbyggda. Därför behöver vissa instansnivådata inte migreras, till exempel underhållsjobb för regelbundna säkerhetskopior eller Alltid på konfiguration, eftersom [hög tillgänglighet](sql-database-high-availability.md) är inbyggd.

Hanterad instans stöder följande alternativ för databasmigrering (för närvarande är dessa de enda migreringsmetoder som stöds):

- Azure Database Migration Service - migrering med nära noll driftstopp,
- Native `RESTORE DATABASE FROM URL` - använder inbyggda säkerhetskopior från SQL Server och kräver vissa driftstopp.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service (DMS)](../dms/dms-overview.md) är en fullständigt hanterad tjänst som utformats för att möjliggöra sömlösa migreringar från flera databaskällor till Azure Data-plattformar med minimal stilleståndstid. Den här tjänsten effektiviserar de uppgifter som krävs för att flytta befintliga tredjeparts- och SQL Server-databaser till Azure. Distributionsalternativen vid offentlig förhandsversion omfattar databaser i Azure SQL Database- och SQL Server-databaser i en virtuell Azure-dator. DMS är den rekommenderade migreringensmetoden för företagets arbetsbelastningar.

Om du använder SQL Server Integration Services (SSIS) på din SQL Server lokalt, stöder DMS ännu inte migrering av SSIS-katalog (SSISDB) som lagrar SSIS-paket, men du kan etablera Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) som skapar en ny SSISDB i en hanterad instans och sedan kan du distribuera om dina paket till den, se [Skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Mer information om det här scenariot och konfigurationsstegen för DMS finns i [Migrera din lokala databas till hanterad instans med DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Intern återställning från URL

ÅTERSTÄLLNING av inbyggda säkerhetskopior (.bak-filer) som tas från SQL Server lokalt eller [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/), som är tillgängligt på Azure [Storage](https://azure.microsoft.com/services/storage/), är en av de viktigaste funktionerna i distributionsalternativet för hanterade instanser som möjliggör snabb och enkel offlinedatabasmigrering.

Följande diagram ger en översikt på hög nivå av processen:

![migrationsflöde](./media/sql-database-managed-instance-migration/migration-flow.png)

I följande tabell finns mer information om de metoder du kan använda beroende på vilken SQL Server-version du kör:

|Steg|SQL Engine och version|Säkerhetskopiering / Återställningsmetod|
|---|---|---|
|Placera säkerhetskopiering till Azure Storage|Tidigare SQL 2012 SP1 CU2|Ladda upp .bak-filen direkt till Azure-lagring|
||2012 SP1 CU2 - 2016|Direkt säkerhetskopiering med föråldrad [med autentiseringsuppgifter](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntax|
||2016 och uppåt|Direkt säkerhetskopiering med [MED SAS-AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Återställa från Azure-lagring till hanterad instans|[ÅTERSTÄLLA FRÅN URL MED SAS-AUTENTISERINGSUPPGIFTER](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - När du migrerar en databas som skyddas av [transparent datakryptering](transparent-data-encryption-azure-sql.md) till en hanterad instans med hjälp av det inbyggda återställningsalternativet måste motsvarande certifikat från den lokala eller IaaS SQL Server migreras innan databasåterställning återställs. Detaljerade steg finns i [Migrera TDE-certifikat till hanterad instans](sql-database-managed-instance-migrate-tde-certificate.md)
> - Återställning av systemdatabaser stöds inte. Om du vill migrera instansnivåobjekt (lagrade i huvud- eller msdb-databaser) rekommenderar vi att du skriver ut dem och kör T-SQL-skript på målinstansen.

En snabbstart som visar hur du återställer en säkerhetskopia av databasen till en hanterad instans med hjälp av en SAS-autentiseringsuppgifter finns i [Återställ från säkerhetskopiering till en hanterad instans](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Övervakning av program

När du har slutfört migreringen till Hanterad instans bör du spåra programbeteendet och prestanda för din arbetsbelastning. Denna process omfattar följande aktiviteter:
- [Jämför prestanda för arbetsbelastningen som körs på den hanterade instansen](#compare-performance-with-the-baseline) med [prestandabaslinjen som du skapade på källan SQL Server](#create-performance-baseline).
- Övervaka kontinuerligt [prestanda för din arbetsbelastning](#monitor-performance) för att identifiera potentiella problem och förbättringar.

### <a name="compare-performance-with-the-baseline"></a>Jämför prestanda med baslinjen

Den första aktiviteten som du skulle behöva ta direkt efter lyckad migrering är att jämföra arbetsbelastningens prestanda med baslinjearbetsbelastningens prestanda. Målet med den här aktiviteten är att bekräfta att arbetsbelastningsprestandan för din hanterade instans uppfyller dina behov. 

Databasmigrering till hanterad instans behåller databasinställningarna och dess ursprungliga kompatibilitetsnivå i de flesta fall. De ursprungliga inställningarna bevaras där det är möjligt för att minska risken för vissa prestandaförsämring jämfört med din källa SQL Server. Om kompatibilitetsnivån för en användardatabas var 100 eller högre före migreringen förblir den densamma efter migreringen. Om kompatibilitetsnivån för en användardatabas var 90 före migreringen är kompatibilitetsnivån i den uppgraderade databasen 100, vilket är den lägsta kompatibilitetsnivån som stöds i hanterade instanser. Kompatibilitetsnivån för systemdatabaser är 140. Eftersom migrering till hanterad instans faktiskt migrerar till den senaste versionen av SQL Server Database Engine, bör du vara medveten om att du måste testa prestanda för din arbetsbelastning för att undvika några överraskande prestandaproblem.

Som en förutsättning, se till att du har slutfört följande aktiviteter:
- Justera inställningarna på hanterad instans med inställningarna från källan SQL Server-instansen genom att undersöka olika instanser, databaser, temdb-inställningar och konfigurationer. Se till att du inte har ändrat inställningar som kompatibilitetsnivåer eller kryptering innan du kör den första prestandajämförelsen, eller acceptera risken för att vissa av de nya funktionerna som du har aktiverat kan påverka vissa frågor. Om du vill minska migreringsriskerna ändrar du databaskompatibilitetsnivån först efter prestandaövervakning.
- Implementera [riktlinjer för bästa praxis för lagring för allmänt ändamål,](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) till exempel förallokering av filernas storlek för att få bättre prestanda.
- Lär dig mer om de [viktigaste miljöskillnaderna som kan orsaka prestandaskillnader mellan hanterad instans och SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) och identifiera de risker som kan påverka prestanda.
- Kontrollera att du behåller aktiverad Query Store och Automatisk justering på din hanterade instans. Med de här funktionerna kan du mäta arbetsbelastningsprestanda och automatiskt åtgärda potentiella prestandaproblem. Lär dig hur du använder Query Store som ett optimalt verktyg för att få information om arbetsbelastningsprestanda före och efter ändringen av databaskompatibilitetsnivå, vilket förklaras i [Behåll prestandastabilitet under uppgraderingen till nyare SQL Server-version](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
När du har förberett miljön som är jämförbar så mycket som möjligt med din lokala miljö kan du börja köra din arbetsbelastning och mäta prestanda. Mätprocessen bör innehålla samma parametrar som du [mätte när du skapar baslinjeprestanda för dina arbetsbelastningsmått på källan SQL Server](#create-performance-baseline).
Därför bör du jämföra prestandaparametrar med baslinjen och identifiera kritiska skillnader.

> [!NOTE]
> I många fall skulle du inte kunna få exakt matchande prestanda på hanterad instans och SQL Server. Hanterad instans är en SQL Server-databasmotor, men infrastruktur och konfiguration med hög tillgänglighet på hanterad instans kan medföra en viss skillnad. Du kan förvänta dig att vissa frågor skulle vara snabbare medan vissa andra kan vara långsammare. Målet med jämförelsen är att kontrollera att arbetsbelastningsprestanda i hanterad instans matchar prestanda på SQL Server (i genomsnitt) och identifiera några kritiska frågor med prestanda som inte matchar dina ursprungliga prestanda.

Resultatet av resultatjämnställningen kan vara:
- Arbetsbelastningsprestanda på hanterad instans är justerad eller bättre än arbetsbelastningsprestandan på SQL Server. I det här fallet har du bekräftat att migreringen lyckas.
- Majoriteten av prestandaparametrarna och frågorna i arbetsbelastningen fungerar bra, med vissa undantag med försämrad prestanda. I det här fallet skulle du behöva identifiera skillnaderna och deras betydelse. Om det finns några viktiga frågor med försämrad prestanda bör du undersöka om de underliggande SQL-abonnemangen har ändrats eller om frågorna når vissa resursgränser. Begränsning i det här fallet kan vara att tillämpa vissa tips om kritiska frågor (till exempel ändrad kompatibilitetsnivå, äldre kardinalitetsuppskattning) antingen direkt eller med hjälp av planguider, återskapa eller skapa statistik och index som kan påverka planerna. 
- De flesta frågor är långsammare på Hanterad instans jämfört med din källa SQL Server. I det här fallet försöka identifiera de grundläggande orsakerna till skillnaden som [att nå vissa resursgräns]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) som IO gränser, minnesgräns, instans logghastighet gräns, etc. Om det inte finns några resursgränser som kan orsaka skillnaden kan du försöka ändra databasens kompatibilitetsnivå eller ändra databasinställningar som äldre kardinalitetsuppskattning och starta testet igen. Granska rekommendationerna från hanterade instans- eller Frågearbetarvyer för att identifiera de frågor som har gått tillbaka prestanda.

> [!IMPORTANT]
> Hanterad instans har inbyggd automatisk plankorrigeringsfunktion som är aktiverad som standard. Den här funktionen säkerställer att frågor som fungerade bra i klistra in inte skulle försämras i framtiden. Kontrollera att den här funktionen är aktiverad och att du har kört arbetsbelastningen tillräckligt länge med de gamla inställningarna innan du ändrar nya inställningar för att aktivera hanterad instans för att lära dig mer om baslinjeprestanda och planer.

Gör ändringen av parametrarna eller uppgraderingstjänstnivåerna för att konvergera till den optimala konfigurationen tills du får den arbetsbelastningsprestanda som passar dina behov.

### <a name="monitor-performance"></a>Övervaka prestanda

Hanterad instans innehåller många avancerade verktyg för övervakning och felsökning, och du bör använda dem för att övervaka prestanda på din instans. Några av de parametrar som du skulle behöva övervaka är:
- CPU-användning på instansen för att avgöra gör antalet virtuella kärnor som du etablerat är rätt matchning för din arbetsbelastning.
- Förväntad sidlivslängd på din hanterade instans för att avgöra [om du behöver ytterligare minne](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Vänta statistik `INSTANCE_LOG_GOVERNOR` som `PAGEIOLATCH` eller som kommer att berätta har du lagring IO frågor, särskilt på general purpose nivå där du kan behöva förallokera filer för att få bättre IO-prestanda.

## <a name="leverage-advanced-paas-features"></a>Utnyttja avancerade PaaS-funktioner

När du är på en fullständigt hanterad plattform och du har verifierat att arbetsbelastningsprestanda matchar dig SQL Server-arbetsbelastningsprestanda, dra nytta av dem som tillhandahålls automatiskt som en del av SQL Database-tjänsten. 

Även om du inte gör vissa ändringar i hanterad instans under migreringen, finns det stora chanser att du aktiverar några av de nya funktionerna medan du använder din instans för att dra nytta av de senaste förbättringarna av databasmotorn. Vissa ändringar aktiveras först när [databaskompatibilitetsnivån har ändrats](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Du behöver till exempel inte skapa säkerhetskopior på hanterad instans - tjänsten utför säkerhetskopior för dig automatiskt. Du behöver inte längre oroa dig för schemaläggning, tagande och hantering av säkerhetskopior. Hanterad instans ger dig möjlighet att återställa till valfri tidpunkt inom denna kvarhållningsperiod med [pitr (Point in Time Recovery).](sql-database-recovery-using-backups.md#point-in-time-restore) Dessutom behöver du inte oroa dig för att konfigurera hög tillgänglighet eftersom [hög tillgänglighet](sql-database-high-availability.md) är inbyggd.

För att stärka säkerheten, överväg att använda [Azure Active Directory Authentication](sql-database-security-overview.md), Auditing, [hotidentifiering,](sql-database-advanced-data-security.md)säkerhet på [radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)och [dynamisk datamaskering](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ). [Auditing](sql-database-managed-instance-auditing.md)

Förutom avancerade hanterings- och säkerhetsfunktioner innehåller Managed Instance en uppsättning avancerade verktyg som kan hjälpa dig att [övervaka och justera din arbetsbelastning.](sql-database-monitor-tune-overview.md) [Med Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) kan du övervaka en stor uppsättning hanterade instanser och centralisera övervakningen av ett stort antal instanser och databaser. [Automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) i hanterad instans övervakar kontinuerligt prestanda för din SQL-plankörningsstatistik och åtgärdar automatiskt de identifierade prestandaproblemen.

## <a name="next-steps"></a>Nästa steg

- Information om hanterade instanser finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- En självstudiekurs som innehåller en återställning från säkerhetskopia finns i [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- Självstudiekurs som visar migrering med DMS finns i [Migrera din lokala databas till hanterad instans med DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
