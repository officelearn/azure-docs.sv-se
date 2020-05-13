---
title: Migrera från SQL Server till hanterad instans
description: Lär dig hur du migrerar en databas från SQL Server instans till Azure SQL Database-Hanterad instans.
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
ms.openlocfilehash: aeee7558aeeb0c1a3de291abc66578d7d955d842
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196187"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server instans migrering till Azure SQL Database Hanterad instans

I den här artikeln får du lära dig mer om metoderna för att migrera en SQL Server 2005-instans eller senare version till [Azure SQL Database Hanterad instans](sql-database-managed-instance.md). Information om hur du migrerar till en enskild databas eller elastisk pool finns i [migrera till en databas med en eller flera](sql-database-cloud-migrate.md)databaser. Information om migrering om migrering från andra plattformar finns i [guiden Migrera Azure Database](https://datamigration.microsoft.com/).

> [!NOTE]
> Om du snabbt vill starta och testa hanterade instanser kanske du vill gå till [snabb starts guide](sql-database-managed-instance-quickstart-guide.md) i stället för den här sidan. 

På en hög nivå ser databasens migreringsprocessen ut så här:

![migreringsprocessen](./media/sql-database-managed-instance-migration/migration-process.png)

- [Utvärdera kompatibilitet för hanterad instans](#assess-managed-instance-compatibility) där du bör se till att det inte finns några spärrnings problem som kan förhindra migreringen.
  - I det här steget ingår även att skapa [bas linje för prestanda](#create-performance-baseline) för att fastställa resursanvändningen på din käll SQL Server instans. Det här steget behövs om du vill distribuera korrekt storleks hanterad instans och kontrol lera att prestandan efter migreringen inte påverkas.
- [Välj anslutnings alternativ för app](sql-database-managed-instance-connect-app.md)
- [Distribuera till en hanterad instans med optimal storlek](#deploy-to-an-optimally-sized-managed-instance) där du väljer tekniska egenskaper (antal virtuella kärnor, mängd minne) och prestanda nivå (affärskritisk generell användning) för din hanterade instans.
- [Välj migreringsprocessen och migrera](#select-migration-method-and-migrate) var du migrerar dina databaser med hjälp av offline-migrering (intern säkerhets kopiering/återställning, databas-/export) eller online-migrering (data migration service, Transaktionsreplikering).
- [Övervaka program](#monitor-applications) för att säkerställa att du har förväntat prestanda.

> [!NOTE]
> Information om hur du migrerar en enskild databas till en enskild databas eller elastisk pool finns i [Migrera en SQL Server databas till Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Bedöm hanterad instans-kompatibilitet

Ta först reda på om den hanterade instansen är kompatibel med programmets databas krav. Distributions alternativet för hanterade instanser har utformats för att ge enkel växel och flyttning av de flesta befintliga program som använder SQL Server lokalt eller på virtuella datorer. Du kan dock ibland behöva funktioner eller funktioner som ännu inte stöds och kostnaden för att implementera en lösning är för hög.

Använd [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) för att identifiera potentiella kompatibilitetsproblem som påverkar databas funktioner på Azure SQL Database. Om det finns rapporterade spärrnings problem kan du behöva överväga ett alternativt alternativ, till exempel [SQL Server på virtuella Azure-datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/). Här följer några exempel:

- Om du behöver direkt åtkomst till operativ systemet eller fil systemet, till exempel för att installera tredje part eller anpassade agenter på samma virtuella dator med SQL Server.
- Om du har strikt beroende på funktioner som fortfarande inte stöds, till exempel FileStream/FileTable-, PolyBase-och kors instans transaktioner.
- Om det är absolut nödvändigt att stanna kvar vid en angiven version av SQL Server (2012, t. ex.).
- Om beräknings kraven är mycket lägre än den hanterade instansen (en vCore, till exempel) och databas konsolidering är inte ett acceptabelt alternativ.

Observera att vissa av ändringarna kan påverka arbets Belastningens prestanda om du har löst alla identifierade migrerings block och fortsätter migreringen till en hanterad instans:

- Den obligatoriska fullständiga återställnings modellen och regelbundna automatiserade säkerhets kopierings scheman kan påverka prestanda för dina arbets belastnings-eller underhålls-eller ETL-åtgärder om du regelbundet har använt Enkel/Mass loggad modell eller stoppat säkerhets kopieringar
- Andra konfigurationer på Server-eller databas nivå, till exempel spårnings flaggor eller kompatibilitetsnivå
- Nya funktioner som du använder, till exempel transparent databas kryptering (TDE) eller grupper för automatisk redundans kan påverka CPU-och i/o-användning.

Hanterad instans garanterar 99,99% tillgänglighet även i de kritiska scenarierna, så det går inte att inaktivera den belastning som orsakas av dessa funktioner. Mer information finns i [rotor Saks orsaker som kan orsaka olika prestanda på SQL Server och hanterad instans](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Skapa bas linje för prestanda

Om du behöver jämföra arbets Belastningens prestanda på en hanterad instans med den ursprungliga arbets belastningen som körs på SQL Server måste du skapa en bas linje för prestanda som ska användas för jämförelse. 

Prestanda bas linje är en uppsättning parametrar som genomsnitt/Max CPU-användning, genomsnittlig/högsta disk-i/o-latens, data flöde, IOPS, genomsnittlig/Max sid livs längd förväntad, genomsnittlig Max storlek på tempdb. Du vill ha liknande eller till och med bättre parametrar efter migreringen, så det är viktigt att mäta och registrera bas linje värden för dessa parametrar. Förutom system parametrar måste du välja en uppsättning av de representativa frågorna eller de viktigaste frågorna i arbets belastningen och mäta min/genomsnittlig/högsta varaktighet, CPU-användning för de valda frågorna. Med dessa värden kan du jämföra prestanda för arbets belastningen som körs på en hanterad instans med de ursprungliga värdena på din käll SQL Server.

Några av de parametrar som du behöver mäta i SQL Server-instansen är: 
- [Övervaka CPU-användning på SQL Server-instansen](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) och registrera den genomsnittliga och högsta CPU-användningen.
- [Övervaka minnes användningen på SQL Server-instansen](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) och Bestäm hur mycket minne som används av olika komponenter, t. ex. buffert, planera cache, Column-Store-pool, [minnes intern OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)osv. Dessutom bör du hitta genomsnittliga och högsta värden för sid livs Längdens förväntad minnes prestanda räknare.
- Övervaka disk-i/o-användning på käll SQL Servers instansen med hjälp av [sys. dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Visa eller [prestanda räknare](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Övervaka arbets belastning och fråga prestanda eller din SQL Server instans genom att undersöka vyer för dynamisk hantering eller Frågearkivet om du migrerar från SQL Server 2016 + version. Identifiera Genomsnittlig varaktighet och CPU-användning för de viktigaste frågorna i arbets belastningen för att jämföra dem med de frågor som körs på den hanterade instansen.

> [!Note]
> Om du upptäcker eventuella problem med arbets belastningen på SQL Server, till exempel hög processor användning, konstant minnes belastning, tempdb-eller Parameterisering-problem, bör du försöka lösa dem på käll SQL Servers instansen innan du tar bas linjen och migreringen. Att migrera kända problem till ett nytt system migh orsakar oväntade resultat och ogiltig validering av prestanda jämförelse.

Som ett resultat av den här aktiviteten bör du ha dokumenterade genomsnitts-och topp värden för processor-, minnes-och IO-användning i ditt käll system, samt genomsnittlig och maximal varaktighet och CPU-användning för de dominerande och de mest kritiska frågorna i arbets belastningen. Du bör använda de här värdena senare för att jämföra arbets Belastningens prestanda på en hanterad instans med bas linje prestanda för arbets belastningen på käll SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Distribuera till en hanterad instans med optimal storlek

Den hanterade instansen är skräddarsydd för lokala arbets belastningar som planerar att flytta till molnet. Den introducerar en [ny inköps modell](sql-database-service-tiers-vcore.md) som ger större flexibilitet när du väljer rätt resurs nivå för dina arbets belastningar. I den lokala världen är du förmodligen van att ändra storlek på dessa arbets belastningar med hjälp av fysiska kärnor och IO-bandbredd. Inköps modellen för en hanterad instans baseras på virtuella kärnor, eller "virtuella kärnor", med ytterligare lagring och IO tillgängligt separat. VCore-modellen är ett enklare sätt att förstå dina beräknings krav i molnet jämfört med vad du använder lokalt. Med den nya modellen kan du anpassa mål miljön i molnet till rätt storlek. Några allmänna rikt linjer som kan hjälpa dig att välja rätt tjänst nivå och egenskaper beskrivs här:
- Baserat på den grundläggande processor användningen kan du etablera en hanterad instans som matchar antalet kärnor som du använder på SQL Server, med tanke på att CPU-egenskaperna kan behöva skalas för att matcha [VM-egenskaper där hanterad instans är installerad](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Baserat på den grundläggande minnes användningen väljer [du tjänst nivån som har motsvarande minne](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). Mängden minne kan inte väljas direkt så du måste välja den hanterade instansen med mängden virtuella kärnor som har motsvarande minne (till exempel 5,1 GB/vCore i Gen5). 
- Baserat på bas linjens IO-svars tid för fil under systemet väljer du mellan Generell användning (fördröjning större än 5ms) och Affärskritisk tjänst nivåer (latens under 3 MS).
- Baserat på baseline-genomflöde förallokerar du storleken på data eller loggfiler för att få förväntad IO-prestanda.

Du kan välja beräknings-och lagrings resurser vid distributions tiden och sedan ändra det efteråt utan att använda [Azure Portal](sql-database-scale-resources.md):

![storlek på hanterad instans](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Information om hur du skapar VNet-infrastrukturen och en hanterad instans finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Det är viktigt att hålla ditt mål-VNet och undernät alltid i enlighet med de [hanterade instansens VNet-krav](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Eventuella inkompatibiliteter kan hindra dig från att skapa nya instanser eller att använda de som du redan har skapat. Lär dig mer om att [skapa nya](sql-database-managed-instance-create-vnet-subnet.md) och [Konfigurera befintliga](sql-database-managed-instance-configure-vnet-subnet.md) nätverk.

## <a name="select-migration-method-and-migrate"></a>Välj metod för migrering och migrera

Distributions alternativet för hanterade instanser riktar sig mot användar scenarier som kräver migrering av Mass databas från lokala eller IaaS databas implementeringar. De är optimala när du behöver lyfta och flytta Server delen av programmen som regelbundet använder instans nivå och/eller kors databas funktioner. Om det här är ditt scenario kan du flytta en hel instans till en motsvarande miljö i Azure utan att behöva skapa program på nytt.

Om du vill flytta SQL-instanser måste du planera noggrant:

- Migreringen av alla databaser som måste vara samordnad (som körs på samma instans)
- Migreringen av objekt på instans nivå som programmet är beroende av, inklusive inloggningar, autentiseringsuppgifter, SQL Agent-jobb och-operatörer och utlösare på server nivå.

Hanterad instans är en hanterad tjänst som gör att du kan delegera några av de vanliga DBA-aktiviteterna till plattformen som de är inbyggda i. Därför behöver vissa data på instans nivå inte migreras, till exempel underhålls jobb för regelbundna säkerhets kopieringar eller alltid på konfigurationen, eftersom [hög tillgänglighet](sql-database-high-availability.md) är inbyggt.

Den hanterade instansen stöder följande flyttnings alternativ för databasen (för närvarande de enda metoder som stöds):

- Azure Database Migration Service migrering med nästan noll stillestånds tid,
- Native `RESTORE DATABASE FROM URL` – använder interna säkerhets kopieringar från SQL Server och kräver vissa avbrott.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database migration service (DMS)](../dms/dms-overview.md) är en fullständigt hanterad tjänst som är utformad för att möjliggöra sömlös migrering från flera databas källor till Azure-dataplattformar med minimal stillestånds tid. Den här tjänsten effektiviserar de uppgifter som krävs för att flytta befintliga tredje parter och SQL Server databaser till Azure. Distributions alternativ i offentlig för hands version innehåller databaser i Azure SQL Database och SQL Server databaser på en virtuell Azure-dator. DMS är den rekommenderade metoden för migrering för företagets arbets belastningar.

Om du använder SQL Server Integration Services (SSIS) på din SQL Server lokalt, stöder inte DMS ännu inte migrering av SSIS-katalogen (SSISDB) som lagrar SSIS-paket, men du kan etablera Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) som skapar en ny SSISDB i en hanterad instans och sedan kan distribuera paketen på nytt, se [skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Mer information om det här scenariot och konfigurations stegen för DMS finns i [migrera din lokala databas till hanterad instans med DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Intern återställning från URL

ÅTERSTÄLLNING av interna säkerhets kopieringar (. bak-filer) som tagits från SQL Server lokalt eller [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/)finns på [Azure Storage](https://azure.microsoft.com/services/storage/), är en av de viktigaste funktionerna i distributions alternativet hanterad instans som möjliggör snabb och enkel migrering av databasen.

Följande diagram ger en övergripande översikt över processen:

![migration-flöde](./media/sql-database-managed-instance-migration/migration-flow.png)

Följande tabell innehåller mer information om de metoder som du kan använda, beroende på vilken käll SQL Servers version du kör:

|Steg|SQL-motor och version|Metod för säkerhets kopiering/återställning|
|---|---|---|
|Lägg till säkerhets kopia i Azure Storage|Tidigare SQL 2012 SP1-CU2|Ladda upp. bak-filen direkt till Azure Storage|
||2012 SP1 CU2-2016|Direkt säkerhets kopiering med föråldrad syntax [för autentiseringsuppgift](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 och uppåt|Direkt säkerhets kopiering med [SAS-autentiseringsuppgifter](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Återställ från Azure Storage till hanterad instans|[Återställ från URL med SAS-AUTENTISERINGSUPPGIFTER](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - När du migrerar en databas som skyddas av [Transparent datakryptering](transparent-data-encryption-azure-sql.md) till en hanterad instans med hjälp av intern återställnings alternativ måste motsvarande certifikat från den lokala eller IaaS SQL Server migreras innan databasen återställs. Detaljerade anvisningar finns i [MIGRERA TDE-certifikat till hanterad instans](sql-database-managed-instance-migrate-tde-certificate.md)
> - Det finns inte stöd för återställning av system databaser. För att migrera instans nivå objekt (lagras i Master-eller MSDB-databaser) rekommenderar vi att du skriptar ut dem och kör T-SQL-skript på mål instansen.

En snabb start som visar hur du återställer en databas säkerhets kopia till en hanterad instans med hjälp av SAS-autentiseringsuppgifter finns i [återställa från en säkerhets kopia till en hanterad instans](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Övervakning av program

När du har slutfört migreringen till en hanterad instans bör du spåra programmets beteende och prestanda för din arbets belastning. Den här processen omfattar följande aktiviteter:
- [Jämför prestanda för arbets belastningen som körs på den hanterade instansen](#compare-performance-with-the-baseline) med den [prestanda bas linje som du skapade på käll SQL Server](#create-performance-baseline).
- [Övervaka prestanda i arbets belastningen](#monitor-performance) kontinuerligt för att identifiera potentiella problem och förbättringar.

### <a name="compare-performance-with-the-baseline"></a>Jämför prestanda med bas linjen

Den första aktiviteten som du måste vidta omedelbart efter lyckad migrering är att jämföra arbets Belastningens prestanda med bas linjens prestanda. Målet med den här aktiviteten är att bekräfta att arbets belastnings prestandan på din hanterade instans uppfyller dina behov. 

Migrering av databasen till den hanterade instansen ser till att databas inställningarna och dess ursprungliga kompatibilitetsnivå är i merparten av fallen. De ursprungliga inställningarna bevaras där det är möjligt för att minska risken för viss prestanda försämring jämfört med din käll SQL Server. Om kompatibilitetsnivån för en användar databas var 100 eller högre innan migreringen, förblir den densamma efter migreringen. Om kompatibilitetsnivån för en användar databas var 90 innan migreringen är kompatibilitetsnivån inställd på 100 i den uppgraderade databasen, vilket är den lägsta kompatibilitetsnivå som stöds i den hanterade instansen. Kompatibilitetsnivån för system databaser är 140. Eftersom migreringen till den hanterade instansen i själva verket migreras till den senaste versionen av SQL Server databas motor bör du vara medveten om att du behöver testa prestandan för arbets belastningen igen för att undvika vissa överraskande prestanda problem.

Se till att du har slutfört följande aktiviteter som krav:
- Justera inställningarna på den hanterade instansen med inställningarna från käll SQL Servers instansen genom att undersöka olika instanser, databaser, temdb-inställningar och konfigurationer. Se till att du inte har ändrat inställningar som kompatibilitetsnivå eller kryptering innan du kör den första prestanda jämförelsen eller godkänn risken att några av de nya funktionerna som du har aktiverat kan påverka vissa frågor. Om du vill minska riskerna för migreringen ändrar du kompatibilitetsnivån för databas efter prestanda övervakning.
- Implementera [rikt linjer för metod tips för lagring för generell användning](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) , till exempel för att förallokera filernas storlek för att få bättre prestanda.
- Lär dig mer om de [viktiga miljö skillnaderna som kan orsaka prestanda skillnader mellan hanterade instanser och SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) och identifiera risker som kan påverka prestandan.
- Se till att du behåller det aktiverade Frågearkivet och automatisk justering på din hanterade instans. Med de här funktionerna kan du mäta arbets belastnings prestanda och automatiskt åtgärda de potentiella prestanda problemen. Lär dig hur du använder Query Store som ett optimalt verktyg för att få information om arbets belastnings prestanda före och efter ändring av databasens kompatibilitetsnivå, enligt beskrivningen i [Behåll prestanda stabiliteten under uppgraderingen till nyare SQL Server version](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
När du har för berett miljön som är så mycket som möjligt i din lokala miljö kan du börja köra din arbets belastning och mät prestanda. Mått processen bör inkludera samma parametrar som du mätte [när du skapar bas linje prestanda för dina arbets belastnings mått på käll SQL Server](#create-performance-baseline).
Därför bör du jämföra prestanda parametrarna med bas linjen och identifiera kritiska skillnader.

> [!NOTE]
> I många fall skulle du inte kunna få exakt matchande prestanda på hanterad instans och SQL Server. Den hanterade instansen är en SQL Server databas motor men infrastruktur och hög tillgänglighets konfiguration på en hanterad instans kan orsaka viss skillnad. Du kan förvänta dig att vissa frågor kan vara snabbare medan andra kan vara långsammare. Jämförelse målet är att kontrol lera att arbets belastnings prestandan i den hanterade instansen matchar prestanda på SQL Server (i genomsnitt) och att identifiera är några kritiska frågor med prestanda som inte matchar dina ursprungliga prestanda.

Resultatet av prestanda jämförelsen kan vara:
- Arbets belastnings prestanda på den hanterade instansen är anpassad eller bättre för arbets belastnings prestandan på SQL Server. I det här fallet har du bekräftat att migreringen har slutförts.
- Majoriteten av prestanda parametrarna och frågorna i arbets belastningen fungerar bra, med vissa undantag med försämrade prestanda. I det här fallet skulle du behöva identifiera skillnaderna och deras prioritet. Om det finns några viktiga frågor med försämrade prestanda bör du undersöka att de underliggande SQL-avtalen har ändrats eller att frågorna påträffar vissa resurs gränser. Minskning i det här fallet kan vara att använda några tips i de kritiska frågorna (till exempel ändrad kompatibilitetsnivå, en äldre kardinalation-uppskattning), antingen direkt eller med hjälp av plan guider, återskapa eller skapa statistik och index som kan påverka planerna. 
- De flesta av frågorna är långsammare på den hanterade instansen jämfört med din käll SQL Server. I det här fallet kan du försöka identifiera rotor saken till skillnaden, till exempel att nå ut till [en resurs gräns]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) som IO-gränser, minnes gräns, instans logg hastighets begränsning osv. Om det inte finns några resurs gränser som kan orsaka skillnaden kan du försöka ändra kompatibilitetsnivån för databasen eller ändra databas inställningar som äldre kardinalitet och starta om testet. Granska de rekommendationer som tillhandahålls av Managed instance eller Query Store-vyer för att identifiera de frågor som försämrat prestanda.

> [!IMPORTANT]
> Den hanterade instansen har inbyggd funktion för automatisk plan korrigering som är aktive rad som standard. Den här funktionen säkerställer att frågor som fungerat bra i inklistring inte försämras i framtiden. Se till att den här funktionen är aktive rad och att du har kört arbets belastningen tillräckligt lång med de gamla inställningarna innan du ändrar nya inställningar för att aktivera hanterad instans för att lära dig om bas linje prestanda och planer.

Ändra parametrarna eller uppgradera tjänst nivåerna så att de konvergerar till den optimala konfigurationen tills du får den arbets belastnings prestanda som passar dina behov.

### <a name="monitor-performance"></a>Övervaka prestanda

Hanterad instans innehåller många avancerade verktyg för övervakning och fel sökning, och du bör använda dem för att övervaka prestanda på din instans. Några av parametrarna som skulle behöva övervakas är:
- CPU-användning på instansen för att avgöra hur många virtuella kärnor som du har allokerat är den rätta matchningen för din arbets belastning.
- Förväntad på din hanterade instans för att fastställa [behöver du ytterligare minne](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Vänta med statistik `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` , som kan se att du har lagrings-i/o-problem, särskilt på generell användning nivå där du kan behöva förallokera filer för att få bättre IO-prestanda.

## <a name="leverage-advanced-paas-features"></a>Utnyttja avancerade PaaS-funktioner

När du är på en helt hanterad plattform och har verifierat att arbets belastnings prestandan överensstämmer SQL Server arbets belastnings prestanda, tar du fördelar som tillhandahålls automatiskt som en del av SQL Databases tjänsten. 

Även om du inte gör några ändringar i den hanterade instansen under migreringen, finns det stora chanser att du kan aktivera några av de nya funktionerna när du kör din instans för att dra nytta av de senaste förbättringarna i databas motorn. Vissa ändringar aktive ras endast när [kompatibilitetsnivån för databas har ändrats](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Du behöver till exempel inte skapa säkerhets kopior på en hanterad instans – tjänsten utför säkerhets kopieringar automatiskt. Du behöver inte längre bekymra dig om schemaläggning, säkerhets kopiering och hantering av säkerhets kopior. Med hanterad instans kan du återställa till vilken tidpunkt som helst inom denna kvarhållningsperiod med hjälp av återställning av tidpunkt [(PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Dessutom behöver du inte bekymra dig om att konfigurera hög tillgänglighet eftersom [hög tillgänglighet](sql-database-high-availability.md) är inbyggt.

För att förstärka säkerheten bör du överväga att använda [Azure Active Directory autentisering](sql-database-security-overview.md), [granskning](sql-database-managed-instance-auditing.md), [hot identifiering](sql-database-advanced-data-security.md), [säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)och [dynamisk data maskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) .

Förutom avancerade hanterings-och säkerhetsfunktioner innehåller hanterad instans en uppsättning avancerade verktyg som kan hjälpa dig att [övervaka och finjustera din arbets belastning](sql-database-monitor-tune-overview.md). Med [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) kan du övervaka en stor uppsättning hanterade instanser och centralisera övervakningen av ett stort antal instanser och databaser. [Automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) i hanterade instanser övervakar kontinuerligt prestanda i SQL Plans körnings statistik och korrigerar automatiskt de identifierade prestanda problemen.

## <a name="next-steps"></a>Nästa steg

- Information om hanterade instanser finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- En själv studie kurs som innehåller en återställning från en säkerhets kopia finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- För självstudier som visar migrering med DMS, se [migrera din lokala databas till hanterad instans med DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
