---
title: Migrera SQL Server-instansen till Azure SQL-hanterade databasinstans | Microsoft Docs
description: "Lär dig mer om att migrera en SQL Server-instans till hanterade Azure SQL Database-instans."
keywords: databasmigrering, sql server-databasmigrering, databasmigreringsverktyg, migrera databas, migrera sql-databas
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: cguyer
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: bonova
ms.openlocfilehash: 44a02ac20a9e9e5fb7171d28e4852ab1a6e63881
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server-instansen migrering till Azure SQL-hanterade databasinstans

I den här artikeln får du lära dig om metoder för att migrera en SQL Server 2005 eller senare version till Azure SQL-hanterade databasinstans (förhandsversion). 

> [!NOTE]
> Om du vill migrera en databas till en enskild databas eller en elastisk pool, se [migrera en SQL Server-databas till Azure SQL Database](sql-database-cloud-migrate.md).

SQL Database Managed Instance är en utökning av den befintliga SQL Database-tjänsten som tillhandahåller ett tredje distributionsalternativ utöver enskilda databaser och elastiska pooler.  Den är utformad för att aktivera databasen lift-och-skifte till en helt hanterad PaaS utan omformandet programmet. SQL Database Managed Instance tillhandahåller hög kompatibilitet med programmeringsmodellen för lokalt installerad SQL Server och direkt stöd för en majoritet av SQL Server-funktionerna och medföljande verktyg och tjänster.

På hög nivå ser programmet migreringsprocessen ut på följande diagram:

![migreringsprocessen](./media/sql-database-managed-instance-migration/migration-process.png)

## <a name="assess-managed-instance-compatibility"></a>Utvärdera hanteras instans kompatibilitet

Bestäm först om hanteras instansen är kompatibel med databaskrav för programmet. Hanterade instans har utformats för att enkelt lift SKIFT migreringen och för flesta befintliga program som använder SQL Server lokalt eller på virtuella datorer. Men du kan ibland kräva funktioner eller funktioner som ännu inte stöds och kostnaden för att implementera en lösning är för högt. 

Använd [Data migrering Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) för att identifiera potentiella kompatibilitetsproblem påverkar databasfunktioner på Azure SQL Database. DMA stöder ännu inte hanteras instans som mål för migrering, men det rekommenderas att köra assessment mot Azure SQL Database och noggrant gå igenom listan över rapporterade funktionsparitet och kompatibilitetsproblem mot produktdokumentationen. De flesta av blockeringsproblem som hindrar en migrering till Azure SQL Database har tagits bort med hanterade instans. För instansen, funktioner som mellan databasfrågor, flera databaser transaktioner inom samma instans länkad server till andra SQL källor, CLR, globala temporära tabeller, finns instans nivån vyer, Service Broker och liknande i hanterade instanser. 

Det finns dock tillfällen när du behöver överväga ett alternativ som [SQL Server på virtuella datorer i Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Här följer några exempel:

- Om du kräver direkt åtkomst till operativsystemet eller filsystem, till exempel installera tredje part eller anpassade agenter på samma virtuella dator med SQL Server.
- Om du har strikt beroende på funktioner som fortfarande inte stöds, till exempel FileStream / filetable-objekt och PolyBase mellan instans transaktioner.
- Om absolut måste du stanna kvar på en viss version av SQL Server (2012, till exempel).
- Om dina behov är mycket lägre hanteras instansen kan som förhandsversion (ett vCore för instans) och konsolidering av databasen inte är acceptabel alternativ.

## <a name="choose-app-connectivity-option"></a>Välj alternativet för app-anslutning

Hanterade instansen finns helt i ditt virtuella nätverk, så att den ger den bästa nivån med isolering och säkerhet för dina data. Följande diagram visar flera alternativ för att distribuera programmet olika topologier helt i Azure eller i en hybridmiljö, oavsett om du väljer en helt hanterad tjänst eller värdbaserad modell för frontend-program.

![programmet distributionstopologier](./media/sql-database-managed-instance-migration/application-deployment-topologies.png)

Några av de valda alternativen tillåter anslutningar till en SQL-slutpunkt endast via privata IP-adresser som garanterar optimal nivå för isolering för dina data. <!--- For more information, see How to connect your application to Managed Instance.--->

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Distribuera till en optimal storlek hanteras instans

Hanterade instans är skräddarsydda för lokala arbetsbelastningar som planerar att flytta till molnet. Det inför en ny inköpsmodell som ger större flexibilitet när du väljer rätt nivå av resurser för din arbetsbelastning. I den lokala världen är du antagligen van vid att ändra storlek på dessa arbetsbelastningar med hjälp av fysiska kärnor. Nya inköpsmodell för hanterade instansen baseras på virtuella kärnor, eller ”vCores” med ytterligare lagringsutrymme och IO tillgänglig separat. VCore modellen är ett enklare sätt att förstå dina behov i molnet jämfört med vad du använder lokala idag. Den nya modellen kan du få rätt storlek mål miljön i molnet.

Du kan välja beräkning och lagringsresurser på distributionen tid och ändra det senare utan att avbrottstid för ditt program.

![hanterade instans storlek](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Information om hur du skapar virtuella nätverk, infrastruktur och hanterade instans - och återställa en databas från en säkerhetskopia finns [skapa en instans för hanterade](sql-database-managed-instance-tutorial-portal.md).

> [!IMPORTANT]
> Det är viktigt att hålla dina mål VNet och undernät alltid i enlighet med [hanteras instans VNET krav](sql-database-managed-instance-vnet-configuration.md#requirements). Någon inkompatibilitet kan hindra dig från att skapa nya instanser eller med de som du redan skapat.

## <a name="select-migration-method-and-migrate"></a>Välj migreringsmetod och migrera

Hanterade instans mål användarscenarier som kräver masslagring Databasmigrering från lokala eller IaaS databasen implementeringar. De är optimala valet när du vill lyfta och flytta serverdelen av programmen som regelbundet använder instansnivå och / eller funktioner för flera databaser. Om det här är ditt scenario kan du flytta en hel instans till en motsvarande miljö i Azure utan att behöva rearchitecture dina program. 

Om du vill flytta SQL-instanser måste du planera noggrant:

-   Migrering av alla databaser som måste vara samordnad (som körs på samma instans)
-   Migrering av objekt på instansnivå som programmet är beroende av, inklusive inloggningar, autentiseringsuppgifter, SQL-Agentjobb och operatörer och serverutlösare. 

Hanterade instansen är en helt hanterad tjänst som hjälper dig att delegera vissa av DBA vanliga aktiviteter för plattformen som de är inbyggda i. Därför vissa nivån instansdata behöver inte migreras, till exempel Underhållsjobb för regelbundna säkerhetskopieringar och alltid på konfiguration som [hög tillgänglighet](sql-database-high-availability.md) är inbyggd i.

Hanterade instans har stöd för följande flera databasen migreringsalternativ:

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

Den [Azure databas migrering Service (DMS)](../dms/dms-overview.md) är en helt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure Data plattformar med minimal avbrottstid. Den här tjänsten förenklar uppgifter som krävs för att flytta befintliga tredjeparts- och SQL Server-databaser till Azure. Distributionsalternativ i Public Preview innehåller Azure SQL Database, hanteras instans och SQL Server i en virtuell dator i Azure. DMS är den rekommenderade metoden för migrering för ditt företagsarbetsbelastningar. 

![DMS](./media/sql-database-managed-instance-migration/dms.png)

Läs mer om det här scenariot och konfigurationssteg för DMS i [migrera dina lokala databasen till hanterade instans med DMS](../dms/tutorial-sql-server-to-azure-sql.md).  

### <a name="native-restore-from-url"></a>Intern återställning från en URL

ÅTERSTÄLLNING av ursprunglig säkerhetskopieringar (.bak-filer) tas från SQL Server lokalt eller [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/), tillgänglig på [Azure Storage](https://azure.microsoft.com/services/storage/), är en av viktiga funktioner för SQL DB hanteras-instans som möjliggör snabb och enkel offline databas migrering. 

Följande diagram beskrivs processen på hög nivå:

![flödet för migrering](./media/sql-database-managed-instance-migration/migration-flow.png)

I följande tabell finns mer information om den metod som du kan använda beroende på källan SQL Server-version du kör:

|Steg|SQL-motorn och version|Säkerhetskopiera / återställa metod|
|---|---|---|
|Placera säkerhetskopiering till Azure Storage|Tidigare SQL 2012 SP1 CU2|Ladda upp bak-filen direkt till Azure storage|
||2012 SP1 CU2 - 2016|Direkt säkerhetskopiering med hjälp av föråldrade [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntax|
||2016 och senare|Direkt säkerhetskopiering med [med SAS-AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Återställa från Azure Storage till hanterade instans|[ÅTERSTÄLLA från URL: en med SAS-AUTENTISERINGSUPPGIFTER](sql-database-managed-instance-tutorial-portal.md#restore-the-wide-world-importers-database-from-a-backup-file)|

> [!IMPORTANT]
> PRestore för systemdatabaser stöds inte. Om du vill migrera instans initialisera (som lagras i master-databasen eller msdb-databaser) rekommenderar vi att skript ut dem och köra T-SQL-skript på mål-instans.

En fullständig genomgång som innehåller återställa en säkerhetskopia av databasen till en hanterad instans med hjälp av en SAS-autentiseringsuppgifter finns [skapa en instans för hanterade](sql-database-managed-instance-tutorial-portal.md).

### <a name="migrate-using-bacpac-file"></a>Migrera med hjälp av BACPAC fil

Du kan importera till Azure SQL Database och hanteras instans från en skapa en kopia av den ursprungliga databasen med data i en BACPAC-fil. Se [importera en BACPAC-fil till en ny Azure SQL Database](sql-database-import.md).

## <a name="monitor-applications"></a>Övervakning av program

Spårar programmets beteende och prestanda efter migreringen. I hanterade instans vissa ändringar aktiveras bara en gång i [databasens kompatibilitetsnivå har ändrats](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Databasmigrering till Azure SQL Database behåller sina ursprungliga kompatibilitetsnivå i de flesta fall. Om kompatibilitetsnivån för en användardatabas 100 eller högre innan migreringen, är densamma efter migreringen. Om kompatibilitetsnivån för en användardatabas var 90 innan migreringen i den uppgraderade databasen anges kompatibilitetsnivån till 100, vilket är lägsta stöds kompatibilitetsnivån i hanterade instans. Kompatibilitetsnivån för systemdatabaser är 140.

Ändra databasens kompatibilitetsnivån efter prestandaövervakning för att minska riskerna för migrering. Använd Query Store som optimala verktyg för att hämta information om arbetsbelastningsprestanda före och efter databasen kompatibilitet nivå ändras, enligt beskrivningen i [hålla prestanda stabilitet under uppgraderingen till en nyare version av SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

När du är på en helt hanterad plattform ta fördelar som tillhandahålls automatiskt som en del av tjänsten SQL-databas. Till exempel du behöver inte skapa säkerhetskopior på hanterade instans - tjänsten utför säkerhetskopieringar av du automatiskt. Du inte längre måste bry dig om schemaläggning, tar och hantera säkerhetskopior. Hanterade instans ger dig möjlighet att återställa till valfri punkt i tiden i det här kvarhållning period med [punkt i tiden återställning (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Offentliga förhandsversionen fast kvarhållningsperioden till sju dagar.
Dessutom kan du inte behöver bry dig om hur du konfigurerar hög tillgänglighet som [hög tillgänglighet](sql-database-high-availability.md) är inbyggd i.

Överväg att använda några av de funktioner som är tillgängliga för att stärka säkerheten:
- Azure Active Directory-autentisering på databasnivå
- Granskning och Hotidentifiering kan övervaka aktiviteter
- Kontrollera åtkomst till känsliga och Privilegierade data ([säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) och [dynamisk Datamaskering](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)).

## <a name="next-steps"></a>Nästa steg

- Information om hanterade instansen finns [vad är en hanterad instans?](sql-database-managed-instance.md)
- En självstudiekurs som innehåller en återställning från en säkerhetskopia finns [skapa en instans för hanterade](sql-database-managed-instance-tutorial-portal.md).
- Självstudiekurs visar migrering med hjälp av DMS, se [migrera dina lokala databasen till hanterade instans med DMS](../dms/tutorial-sql-server-to-azure-sql.md).  
