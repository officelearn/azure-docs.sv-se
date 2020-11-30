---
title: SQL Server SQL Server på Azure VM (översikt över migrering)
description: Lär dig mer om de olika migrerings strategierna när du vill migrera dina SQL Server till SQL Server på virtuella Azure-datorer.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 4979902853602073e6230ef7387d6c6596fe77da
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325934"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Översikt över migrering: SQL Server till SQL Server på virtuella Azure-datorer
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Lär dig mer om de olika migrerings strategierna för att migrera SQL Server till SQL Server på Azure-Virtual Machines (VM). 

Du kan migrera SQL Server som körs lokalt eller på:

- SQL Server på virtuella datorer  
- Amazon Web Services (AWS) EC2 
- Amazon Relations databas tjänst (AWS RDS) 
- Beräknings motor (Google Cloud Platform-GCP)

För andra scenarier, se [Guide för databas migrering](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Översikt

Migrera till [SQL Server på Azure Virtual Machines (VM)](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) om du vill använda den bekanta SQL Server miljön med OS-kontrollen och vill dra nytta av molnbaserade funktioner, till exempel inbyggd virtuell dator med hög tillgänglighet, [automatiserade säkerhets kopieringar](../../virtual-machines/windows/automated-backup.md)och [automatiserad uppdatering](../../virtual-machines/windows/automated-patching.md). 

Spara pengar genom att ta med din egen licens med [Azure Hybrid-förmån licens modell](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) eller utöka stödet för SQL Server 2008 och SQL Server 2008 R2 genom att få [kostnads fria säkerhets uppdateringar](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md). 


## <a name="choosing-appropriate-target"></a>Välja lämpligt mål

Azure Virtual Machines köras i många olika regioner i Azure och erbjuder också en mängd olika [maskin storlekar](../../../virtual-machines/sizes.md) och [lagrings alternativ](../../../virtual-machines/disks-types.md). När du fastställer rätt storlek för virtuell dator och lagring för din SQL Server arbets belastning, se [rikt linjer för prestanda för SQL Server på Azure Virtual Machines.](../../virtual-machines/windows/performance-guidelines-best-practices.md#vm-size-guidance) För att fastställa storleken på den virtuella datorn och lagrings kraven för din arbets belastning. Vi rekommenderar att du har en sådan storlek genom en Performance-Based [Azure Migrate utvärdering](../../../migrate/concepts-assessment-calculation.md#types-of-assessments). Om detta inte är ett tillgängligt alternativ kan du läsa följande artikel om hur du skapar en egen [bas linje för prestanda](https://azure.microsoft.com/services/virtual-machines/sql-server/).

Att överväga bör också göras på rätt installation och konfiguration av SQL Server på en virtuell dator. Vi rekommenderar att du använder [avbildnings galleriet för virtuella Azure SQL-datorer](../../virtual-machines/windows/create-sql-vm-portal.md) eftersom det gör att du kan skapa en SQL Server VM med rätt version, utgåva och operativ system. Detta registrerar även den virtuella Azure-datorn med SQL Server [Resource Provider](../../virtual-machines/windows/create-sql-vm-portal.md) automatiskt, vilket möjliggör funktioner som automatiserade säkerhets kopieringar och automatiserad uppdatering.

## <a name="migration-strategies"></a>Migreringsstrategier

Det finns två migrations strategier för att migrera dina användar databaser till en instans av SQL Server på virtuella Azure-datorer: **migrera** och **lyft och flytta**. 

Lämplig metod för din verksamhet beror vanligt vis på följande faktorer: 

- Storlek och skala för migrering
- Snabb migrering
- Program stöd för kod ändring
- Du måste ändra SQL Server version, operativ system eller både och.
- Support livs cykel för befintliga produkter
- Fönster för program-nedtid under migrering

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="stillestånds tid för migrering av virtuell dator":::

I följande tabell beskrivs skillnaderna i de två migrations strategierna:
<br />

| **Migreringsstrategi** | **Beskrivning** | **När du ska använda detta** |
| --- | --- | --- |
| **Lyft & Shift** | Använd den nya strategin för att flytta och flytta hela den fysiska eller virtuella SQL Server från den aktuella platsen till en instans av SQL Server på den virtuella Azure-datorn utan ändringar i operativ systemet, eller SQL Server version. Information om hur du utför en hiss och Shift-migrering finns [Azure Migrate](../../../migrate/migrate-services-overview.md). <br /><br /> Käll servern är online-och tjänst begär anden när käll-och mål servern synkroniserar data som möjliggör en nästan Sömlös migrering. | Använd för enstaka till mycket storskalig migrering, även för scenarier som data Center avslutar. <br /><br /> Minimalt antal ändringar som krävs för användarens SQL-databaser eller program, vilket möjliggör snabbare övergripande migreringar. <br /><br />Inga ytterligare åtgärder krävs för att migrera Business Intelligence-tjänster som  [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)och [SSAS](/analysis-services/analysis-services-overview). |
|**Migrera** | Använd en migrerande strategi när du vill uppgradera mål SQL Server och/eller operativ system versionen. <br /> <br /> Välj en virtuell Azure-dator från Azure Marketplace eller en för beredd SQL Server avbildning som matchar käll SQL Server versionen. | Använd när det finns ett krav eller vill använda funktioner som är tillgängliga i nyare versioner av SQL Server, eller om det finns ett krav för att uppgradera äldre SQL Server och/eller OS-versioner som inte längre stöds.  <br /> <br /> Kan kräva att vissa program-eller användar databas ändringar stöder SQL Server uppgraderingen. <br /><br />Det kan finnas ytterligare överväganden för att migrera [Business Intelligence](#business-intelligence) -tjänster vid migreringens omfång. |


## <a name="lift-and-shift"></a>Lift and Shift  

Följande tabell innehåller information om den tillgängliga metoden för migreringen av **hiss och Shift** för att migrera SQL Server-databasen till SQL Server på virtuella Azure-datorer:
<br />

|**Metod** | **Lägsta käll version** | **Lägsta mål version** | **Storleks begränsning för käll säkerhets kopia** |  **Kommentarer** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Lagrings gräns för Azure VM](../../../index.yml) |  Befintliga SQL Server som ska flyttas som-är till en instans av SQL Server på en virtuell Azure-dator. Kan skala arbets belastningar för migrering av upp till 35 000 virtuella datorer. <br /><br /> Käll servrar är fortfarande online-och etablerings begär Anden under synkroniseringen av Server data, vilket minimerar stillestånds tiden. <br /><br /> **Automation & skript**: [Azure Site Recovery skript](../../../migrate/how-to-migrate-at-scale.md) och [exempel på skalad migrering och planering för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrera  

På grund av den enkla installationen är den rekommenderade migreringen att ta en intern SQL Server [säkerhets kopiering](/sql/t-sql/statements/backup-transact-sql) lokalt och sedan kopiera filen till Azure. Den här metoden stöder större databaser (>1 TB) för alla versioner av SQL Server som börjar från 2008 och större databas säkerhets kopior (>1 TB). Men för databaser som börjar i SQL Server 2014, som är mindre än 1 TB och som har en bra anslutning till Azure, är [SQL Server säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) det bästa sättet. 

När du migrerar SQL Server-databaser till en instans av SQL Server på virtuella Azure-datorer är det viktigt att du väljer en metod som passar när du behöver start punkt till mål servern eftersom detta påverkar fönstret Application nedtid.

Följande tabell innehåller alla tillgängliga metoder för att migrera SQL Server-databasen till SQL Server på virtuella Azure-datorer:
<br />

|**Metod** | **Lägsta käll version** | **Lägsta mål version** | **Storleks begränsning för käll säkerhets kopia** | **Kommentarer** |
| --- | --- | --- | --- | --- |
| **[Säkerhetskopiera till en fil](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Lagrings gräns för Azure VM](../../../index.yml) |  Det här är en enkel och vältestad teknik för att flytta databaser mellan datorer. Använd komprimering för att minimera säkerhets kopierings storlek för överföring. <br /><br /> **Automation & skript**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) och [AzCopy till Blob Storage](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Säkerhetskopiera till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12,8 TB för SQL Server 2016, annars 1 TB | Ett alternativt sätt att flytta säkerhets kopian till den virtuella datorn med Azure Storage. Använd komprimering för att minimera säkerhets kopierings storlek för överföring. <br /><br /> **Automation &-skript**:  [T-SQL eller underhålls plan](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Databas Migration Assistant (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Lagrings gräns för Azure VM](../../../index.yml) |  [DMA](/sql/dma/dma-overview) -tjänsten utvärderar SQL Server lokalt och uppgraderar sedan sömlöst till senare versioner av SQL Server eller migrerar till SQL Server på virtuella Azure-datorer, Azure SQL Database eller Azure SQL-hanterad instans. <br /><br /> Ska inte användas på FILESTREAM-aktiverade användar databaser.<br /><br /> DMA inkluderar även möjlighet att migrera [SQL-och Windows-inloggningar](/sql/dma/dma-migrateserverlogins) och utvärdera [SSIS-paket](/sql/dma/dma-assess-ssis). <br /><br /> **Automation & skript**: [kommando rads gränssnitt](/sql/dma/dma-commandline) |
| **[Koppla från och koppla](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Lagrings gräns för Azure VM](../../../index.yml) | Använd den här metoden när du planerar att [lagra filerna med hjälp av Azure Blob Storage-tjänsten](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) och koppla dem till en instans av SQL Server på en virtuell Azure-dator, särskilt användbart med mycket stora databaser eller när tiden för säkerhets kopiering och återställning är för lång. <br /><br /> **Automation &-skript**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) och [AzCopy till Blob Storage](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Logg överföring](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (endast Windows) | SQL Server 2008 SP4 (endast Windows) | [Lagrings gräns för Azure VM](../../../index.yml) | Logg överföring replikerar transaktionella loggfiler från lokala platser till en instans av SQL Server på en virtuell Azure-dator. <br /><br /> Detta ger minimal nedtid under redundans och har mindre konfigurations kostnader än att konfigurera en tillgänglighets grupp som alltid är tillgänglig. <br /><br /> **Automation &-skript**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Distribuerad tillgänglighets grupp](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Lagrings gräns för Azure VM](../../../index.yml) |  En [distribuerad tillgänglighets grupp](/sql/database-engine/availability-groups/windows/distributed-availability-groups) är en särskild typ av tillgänglighets grupp som sträcker sig över två separata tillgänglighets grupper. Tillgänglighets grupperna som ingår i en distribuerad tillgänglighets grupp behöver inte finnas på samma plats och inkluderar stöd för olika domäner. <br /><br /> Med den här metoden minimeras drift stoppen, när en tillgänglighets grupp har kon figurer ATS lokalt. <br /><br /> **Automation &-skript**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> För stora data överföringar med begränsad till inga nätverks alternativ, se [stora data överföringar med begränsad anslutning](../../../storage/common/storage-solution-large-dataset-low-network.md).
> 

### <a name="considerations"></a>Överväganden

Följande är en lista över viktiga punkter som du bör tänka på när du ska granska migrations metoder:

- För optimal data överförings prestanda migrerar du databaser och filer till en instans av SQL Server på en virtuell Azure-dator med en komprimerad säkerhets kopia. För större databaser kan du förutom komprimering [dela upp säkerhets kopian i mindre filer](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) för ökad prestanda under säkerhets kopiering och överföring. 
- Om du migrerar från SQL Server 2014 eller högre bör du överväga att [kryptera säkerhets kopiorna](/sql/relational-databases/backup-restore/backup-encryption) för att skydda data under nätverks överföring.
- Använd alternativet Always on tillgänglighets grupp om du vill minimera stillestånds tiden under migreringen av databasen. 
- Använd alternativet logg överföring för att minimera drift stopp utan att konfigurera en tillgänglighets grupp. 
- För begränsade till inga nätverks alternativ använder du metoder för offline-migrering, till exempel säkerhets kopiering och återställning, eller [disk överförings tjänster](../../../storage/common/storage-solution-large-dataset-low-network.md) som är tillgängliga i Azure.
- Om du också vill ändra versionen av SQL Server på en SQL Server på den virtuella Azure-datorn, se [ändra SQL Server utgåva](../../virtual-machines/windows/change-sql-server-edition.md).

## <a name="business-intelligence"></a>Business Intelligence 

Det kan finnas ytterligare överväganden när du migrerar SQL Server Business Intelligence-tjänster utanför omfånget för migrering av användar databaser. 

Här är några exempel på sådana tjänster:

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Versioner som stöds

När du förbereder för migrering av SQL Server databaser till SQL Server på virtuella Azure-datorer bör du tänka på vilka versioner av SQL Server som stöds. En lista över aktuella SQL Server-versioner som stöds på virtuella Azure-datorer finns [SQL Server på virtuella Azure-datorer](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).


## <a name="next-steps"></a>Nästa steg

Om du vill börja migrera dina SQL Server-databaser till SQL Server på virtuella Azure-datorer kan du läsa mer i den [enskilda Database migration guide](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- En matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter finns i artikel [tjänsten och verktyg för datamigrering.](../../../dms/dms-tools-matrix.md)

- Mer information om Azure SQL finns i:
   - [Distributionsalternativ](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server på virtuella Azure-datorer](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om licensiering finns i
   - [Bring your own license med Azure Hybrid-förmån](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Få kostnads fri utökad support för SQL Server 2008 och SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).