---
title: Migrera SQL Server-instans till Azure SQL Database Managed Instance | Microsoft Docs
description: Lär dig mer om att migrera en SQL Server-instans till Azure SQL Database Managed Instance.
keywords: databasmigrering, sql server-databasmigrering, databasmigreringsverktyg, migrera databas, migrera sql-databas
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: bonova
ms.openlocfilehash: cf3f7e131b177634318a6114b4f1efefcb9a9cec
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985668"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrering av SQL Server-instans till Azure SQL Database Managed Instance

I den här artikeln får du lära dig om metoder för att migrera en SQL Server 2005 eller senare version instans till [Azure SQL Database Managed Instance](sql-database-managed-instance.md) (förhandsversion).

Migreringen av databasen ut som på en hög nivå:

![Migreringsprocessen](./media/sql-database-managed-instance-migration/migration-process.png)

- [Utvärdera kompatibilitet för hanterad instans](sql-database-managed-instance-migrate.md#assess-managed-instance-compatibility)
- [Välj alternativet för app-anslutning](sql-database-managed-instance-migrate.md#choose-app-connectivity-option)
- [Distribuera till en optimal storlek hanterad instans](sql-database-managed-instance-migrate.md#deploy-to-an-optimally-sized-managed-instance)
- [Välj migreringsmetod och migrera](sql-database-managed-instance-migrate.md#select-migration-method-and-migrate)
- [Övervaka program](sql-database-managed-instance-migrate.md#monitor-applications)

> [!NOTE]
> Om du vill migrera en databas till en enkel databas eller elastisk pool, se [migrera en SQL Server-databas till Azure SQL Database](sql-database-cloud-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Utvärdera kompatibilitet för hanterad instans

Börja med att kontrollera om Managed Instance är kompatibelt med databaskrav av ditt program. Hanterad instans är utformad att ge enkel lift and shift-migrering för flesta av befintliga program som använder SQL Server lokalt eller på virtuella datorer. Men du kan ibland behöva funktioner eller funktioner som ännu inte stöds och kostnaden för att implementera en lösning är för höga. 

Använd [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) för att identifiera potentiella kompatibilitetsproblem påverkar databasfunktionaliteten på Azure SQL Database. DMA har stöd inte för hanterad instans som mål för migrering, men det rekommenderas att köra utvärderingen mot Azure SQL Database och noga igenom listan över rapporterade funktionsparitet och kompatibilitetsproblem mot produktdokumentationen. Se [Azure SQL Database-funktioner](sql-database-features.md) att kontrollera finns det vissa rapporterade allvarliga problem att inte blockeringar i hanterad instans, eftersom de flesta av de blockerar problem hindrar en migrering till Azure SQL Database har tagits bort med hanterad Instans. För instans, funktioner som databasöverskridande frågor och transaktioner över flera databaser inom samma instans länkad server för att andra SQL källor, CLR, globala temporära tabeller, är instans på vyer, Service Broker och liknande tillgängliga i hanterade instanser. 

Om det finns några rapporterade allvarliga problem som inte tas bort i Azure SQL Managed Instance kan du behöva tänka på ett alternativ, till exempel [SQL Server på virtuella datorer i Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Här följer några exempel:

- Om du kräver direkt åtkomst till operativsystemet eller filsystem, till exempel att installera från tredje part eller anpassade agenter på samma virtuella dator med SQL Server.
- Om du har strikta beroende på funktioner som fortfarande inte stöds, till exempel FileStream / filetable-objekt, PolyBase och transaktioner över flera instanser.
- Om absolut måste du hålla sig i en specifik version av SQL Server (2012, till exempel).
- Om dina beräkningskrav är mycket lägre den hanterade instansen kan i offentlig förhandsversion (1 vCore, till exempel) och konsolidering av databasen inte är acceptabel.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Distribuera till en optimal storlek hanterad instans

Hanterad instans skräddarsys för lokala arbetsbelastningar som planerar att flytta till molnet. Det inför en [nya inköpsmodell](sql-database-service-tiers-vcore.md) som ger större flexibilitet att välja rätt nivå av resurser för dina arbetsbelastningar. I den lokala miljön är du antagligen van vid att ändra storlek på dessa arbetsbelastningar med hjälp av fysiska kärnor och i/o-bandbredd. Den nya inköpsmodellen för hanterad instans baseras på virtuella kärnor, eller ”vCores”, med ytterligare lagringsutrymme och I/O som är tillgängliga separat. VCore-modellen är ett enklare sätt att förstå dina beräkningskrav i molnet eller det du använder en lokal idag. Den nya modellen kan du justera storleken din mål-miljö i molnet.

Du kan välja beräknings- och lagringsresurser vid distributionen tid och ändra den senare utan att driftstopp för dina program med hjälp av den [Azure-portalen](sql-database-scale-resources.md):

![hanterad instans-storlek](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Läs hur du skapar infrastrukturen som virtuellt nätverk och en hanterad instans i [skapar en hanterad instans](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Det är viktigt att hålla dina mål VNet och undernät alltid enligt [hanterad instans-VNET-krav](sql-database-managed-instance-vnet-configuration.md#requirements). All inkompatibilitet kan hindra dig från att skapa nya instanser eller använda dem som du redan har skapat.

## <a name="select-migration-method-and-migrate"></a>Välj migreringsmetod och migrera

Hanterade instans mål användarscenarier som kräver drivrutiner för masslagring Databasmigrering från lokala eller IaaS databasen implementeringar. De är optimala val när du vill flytta över serverdelen för de program som regelbundet använder instansnivå och / eller databasöverskridande funktioner. Om det här är ditt scenario kan du flytta en hel instans till en motsvarande miljö i Azure utan att behöva rearchitecture dina program. 

Om du vill flytta SQL-instanser måste du planera noggrant:

-   Migrering av alla databaser som måste vara samordnad (som körs på samma instans)
-   Migrering av på instansnivå objekt som ditt program är beroende av, inklusive inloggningar, autentiseringsuppgifter, SQL-Agentjobb och operatorer och på serverutlösare. 

Managed Instance är en fullständigt hanterad tjänst som gör att du kan delegera vissa av de ordinarie DBA-åtgärder för plattformen som de är inbyggda i. Därför vissa nivå instansdata inte behöver migreras, till exempel Underhållsjobb för regelbundna säkerhetskopieringar eller Always On-konfiguration som [hög tillgänglighet](sql-database-high-availability.md) är inbyggd i.

Hanterad instans stöder följande databas-migreringsalternativ (för närvarande dessa är de enda migreringsstorleken metoderna):

- Azure Database Migration Service - migrering med nästan obefintlig nedtid
- Interna `RESTORE DATABASE FROM URL` – använder interna säkerhetskopieringar från SQL Server och kräver vissa avbrott.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

Den [Azure Database Migration Service (DMS)](../dms/dms-overview.md) är en fullständigt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid. Den här tjänsten effektiviserar uppgifter som krävs för att flytta befintliga från tredje part och SQL Server-databaser till Azure. Distributionsalternativ i den offentliga förhandsversionen inkluderar Azure SQL Database Managed Instance och SQL Server i en Azure virtuell dator. DMS är den rekommenderade metoden för migrering för dina företagsarbetsbelastningar. 

Om du använder SQL Server Integration Services (SSIS) i SQL Server lokalt, DMS stöder ännu inte migrera SSIS-katalogen (SSISDB) som lagrar SSIS-paket, men du kan etablera Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) som kommer Skapa en ny SSISDB i Azure SQL-databas/hanterad instans och sedan du kan distribuera om ditt paket till den, se [skapa Azure-SSIS IR i ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime).

Läs mer om det här scenariot och konfigurationssteg för att DMS i [migrera dina lokala databaser till Managed Instance med DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Intern återställning från URL

ÅTERSTÄLLNING av interna säkerhetskopieringar (.bak-filer) som kommer från SQL Server lokalt eller [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/)är tillgängligt på [Azure Storage](https://azure.microsoft.com/services/storage/), är en av de viktigaste funktionerna på SQL DB Managed Instance som möjliggör snabbt och enkelt offline Databasmigrering. 

Följande diagram ger en översikt över processen:

![migrering flöde](./media/sql-database-managed-instance-migration/migration-flow.png)

Följande tabell innehåller mer information om de metoder som du kan använda beroende på källan SQL Server-version du kör:

|Steg|SQL-motor och version|Säkerhetskopiera / återställa metoden|
|---|---|---|
|Placera säkerhetskopiering till Azure Storage|Tidigare SQL 2012 SP1 CU2|Ladda upp bak-filen direkt till Azure storage|
||2012 SP1 CU2 - 2016|Direkt säkerhetskopiering med inaktuella [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntax|
||2016 och senare|Direkt säkerhetskopiering med [med SAS-AUTENTISERINGSUPPGIFTER](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Återställa från Azure Storage till hanterad instans|[ÅTERSTÄLLA från URL: en med SAS-AUTENTISERINGSUPPGIFTER](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - När du migrerar en databas som skyddas av [transparent datakryptering](transparent-data-encryption-azure-sql.md) till Azure SQL-hanterad instans med hjälp av det inbyggda återställningsalternativet måste det motsvarande certifikatet från en lokal server eller IaaS-SQL-server migreras före databasåterställningen. Detaljerade anvisningar finns i [migrera TDE-certifikat till hanterad instans](sql-database-managed-instance-migrate-tde-certificate.md)
> - Återställning av systemdatabaser stöds inte. Om du vill migrera nivå instansobjekt (lagras i master- eller msdb-databaser), rekommenderar vi att skriva ut dem och köra T-SQL-skript på mål-instans.

En Snabbstart som visar hur du återställer en säkerhetskopia av databasen till en hanterad instans med hjälp av SAS-autentiseringsuppgifter, se [återställa från en säkerhetskopia till en hanterad instans](sql-database-managed-instance-get-started-restore.md).

## <a name="monitor-applications"></a>Övervakning av program

Spåra programmets beteende och prestanda efter migreringen. I Managed Instance vissa ändringar är endast aktiverat när den [kompatibilitetsnivån för databas har ändrats](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Databasmigrering till Azure SQL Database behåller dess ursprungliga kompatibilitetsnivå i de flesta fall. Om en användardatabas kompatibilitetsnivå 100 eller högre före migreringen, förblir densamma efter migreringen. Om en användardatabas kompatibilitetsnivå 90 före migreringen i den uppgraderade databasen anges kompatibilitetsnivå till 100, vilket är lägsta stöds kompatibilitetsnivån i Managed Instance. Kompatibilitetsnivån för systemdatabaser är 140.

Ändra kompatibilitetsnivån för databas för att minska migreringsrisker förrän prestandaövervakning. Använd Query Store som optimala verktyg för att hämta information om arbetsbelastningens prestanda före och efter databas nivåändring för kompatibilitet, enligt beskrivningen i [hålla prestanda stabilitet under uppgraderingen till nyare version av SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

När du är på en helt hanterad plattform ta fördelar som tillhandahålls automatiskt som en del av tjänsten SQL Database. Exempelvis kan du behöver inte skapa säkerhetskopior på hanterad instans - tjänsten utför säkerhetskopieringar åt dig automatiskt. Du måste inte längre tänka på schemaläggning, tar och hantera säkerhetskopior. Hanterad instans ger dig möjlighet att återställa till valfri punkt inom denna kvarhållning period med [peka i tiden Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Kvarhållningsperioden är låst till sju dagar under den offentliga förhandsperioden.
Dessutom kan du inte behöver bekymra dig om hur du konfigurerar hög tillgänglighet som [hög tillgänglighet](sql-database-high-availability.md) är inbyggd i.

Överväg att använda några av de funktioner som är tillgängliga för att stärka säkerheten:
- Azure Active Directory-autentisering på databasnivå
- Använd [avancerade säkerhetsfunktioner](sql-database-security-overview.md) som [granskning](sql-database-managed-instance-auditing.md), [Hotidentifiering](sql-advanced-threat-protection.md), [säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), och [dynamisk Datamaskning](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ) att skydda din instans.

## <a name="next-steps"></a>Nästa steg

- Information om hanterade instanser finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
- Se en självstudie som innehåller en återställning från en säkerhetskopia, [skapar en hanterad instans](sql-database-managed-instance-get-started.md).
- Självstudie som visar migrering med DMS, se [migrera dina lokala databaser till Managed Instance med DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
