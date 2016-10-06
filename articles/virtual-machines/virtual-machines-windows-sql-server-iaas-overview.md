<properties
    pageTitle="Översikt över SQL Server i Azure Virtual Machines | Microsoft Azure"
    description="Läs mer om hur du kör fullständiga SQL Server-utgåvor på virtuella datorer med Azure. Hämta direktlänkar till alla SQL Server VM-avbildningar och tillhörande innehåll."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth"/>


# Översikt över SQL Server i Azure Virtual Machines

Det här ämnet beskriver dina alternativ för att köra SQL Server på virtuella Azure-datorer, tillsammans med [länkar till portalavbildningar](#option-1-deploy-a-sql-vm-per-minute-licensing) och en översikt över [vanliga uppgifter](#manage-your-sql-vm).

>[AZURE.NOTE] Om du redan är bekant med SQL Server och bara vill se hur man distribuerar en virtuell dator med SQL Server, hittar du mer information i [Etablera en virtuell dator med SQL Server på Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

## Översikt
Kanske är du en databasadministratör som vill flytta dina lokala SQL Server-arbetsbelastningar till molnet. Eller kanske är du en utvecklare som överväger att använda relationsdatabasfunktionerna i SQL Server i ditt Azure-program. Vad är fördelen med att köra SQL Server-arbetsbelastningar på virtuella Azure-datorer? I följande översiktsvideo diskuteras de olika fördelarna. Klippet innehåller även en teknisk översikt.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Utvärdera fördelarna

Innan du börjar bör du utvärdera vad du tjänar på att använda SQL Server på virtuella Azure-datorer.

Om du flyttar andra arbetsbelastningar till Azure, till exempel ett företagsprogram, kan det vara bra att även flytta beroende SQL Server-databaser till Azure för bättre prestanda. Men att låta virtuella datorer i Azure vara värdar för SQL Server har andra fördelar. Exempelvis har du automatiskt åtkomst till flera datacenter för global närvaro och haveriberedskap. En fullständig lista över scenarier och fördelar finns på [produktsidan för SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [AZURE.NOTE] När du utvärderar SQL Server på Azure Virtual Machines kan du också titta igenom andra lagrings- och SQL-alternativ i Azure, t.ex. [SQL Database](../sql-database/sql-database-technical-overview.md), [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) och [SQL Server Stretch Database](../sql     -server-stretch-database/sql-server-stretch-database-overview.md). En detaljerad jämförelse finns i [Välja ett molnbaserat SQL Server-alternativ: Azure SQL (PaaS) Database eller SQL Server på Azure Virtual Machines (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

När du har bestämt dig för att köra SQL Server på Azure Virtual Machines är ett av dina första beslut om du vill använda en VM-avbildning som inkluderar SQL Server-licensieringskostnaderna. Ett annat alternativ är att använda din egen licens (BYOL, Bring Your Own License) och endast betala för den virtuella datorn. Dessa alternativ beskrivs i följande två avsnitt.

## Skapa en ny virtuell SQL-dator
Följande avsnitt innehåller direktlänkar till Azure-portalen för galleriavbildningarna för virtuella datorer med SQL Server. Beroende på vilken avbildning du väljer kan du antingen betala för SQL Server-licensen per minut eller använda din egen licens (Bring your own license, BYOL).

Stegvisa anvisningar finns i självstudiekursen [Etablera en virtuell dator med SQL Server på Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Läs också [Prestandarelaterade metodtips för virtuella datorer med SQL Server](virtual-machines-windows-sql-performance.md), som förklarar hur du väljer lämplig datorstorlek och andra funktioner som är tillgängliga vid etablering.

## Alternativ 1: Skapa en virtuell SQL-dator med licensiering per minut
Följande tabell innehåller en matris med tillgängliga SQL Server-avbildningar i galleriet för virtuella datorer. Klicka på en länk om du vill skapa en ny virtuell dator med SQL med den version, den utgåva och det operativsystem du angett.

|Version|Operativsystem|Utgåva|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## Alternativ 2: Skapa en virtuell SQL-dator med en befintlig licens
Du kan även använda din egen licens (Bring your own license, BYOL). I det här scenariot betalar du bara för den virtuella datorn utan ytterligare avgifter för SQL Server-licensiering. Om du vill använda din egen licens använder du matrisen med de versioner, utgåvor och operativsystem för SQL Server som anges nedan. Namnen på avbildningarna föregås av **{BYOL}** i portalen.

|Version|Operativsystem|Utgåva|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] För att kunna använda VM-avbildningar med BYOL måste du ha ett Enterprise-avtal med [licensmobilitet via Software Assurance i Azure](https://azure.microsoft.com/pricing/license-mobility/). Du måste också ha en giltig licens för den version/utgåva av SQL Server som du vill använda. Du måste [förse Microsoft med nödvändig BYOL-information](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) inom **tio** dagar från etableringen av den virtuella datorn.

## Hantera den virtuella SQL-datorn
Det finns flera valfria hanteringsuppgifter som du kan utföra när du har etablerat din virtuella SQL Server-dator. Till stor del konfigurerar du och hanterar SQL Server på exakt samma sätt som när du administrerar en lokal SQL Server-instans. Men vissa uppgifter är specifika för Azure. I följande avsnitt beskrivs några av dessa områden med länkar till mer information.

### Migrera dina data

Om du har en befintlig databas vill du antagligen flytta den till den nyetablerade virtuella SQL-datorn. En lista över migreringsalternativ och anvisningar finns i [Migrera en databas till SQL Server på en virtuell dator i Azure](virtual-machines-windows-migrate-sql.md).

### Konfigurera hög tillgänglighet

Om du behöver hög tillgänglighet bör du överväga att konfigurera SQL Server-tillgänglighetsgrupper. Detta betyder att det finns flera virtuella Azure-datorer i ett virtuellt nätverk. På Azure Portal finns det en mall som ställer in den här konfigurationen åt dig. Mer information finns i [Konfigurera en AlwaysOn-tillgänglighetsgrupp för virtuella datorer i Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Om du vill konfigurera tillgänglighetsgruppen och associerade lyssnare manuellt läser du [Konfigurera AlwaysOn-tillgänglighetsgrupper för virtuella datorer i Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Mer information om hög tillgänglighet finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

### Säkerhetskopiera data
Virtuella Azure-datorer kan dra nytta av [automatisk säkerhetskopiering](virtual-machines-windows-sql-automated-backup.md), som regelbundet skapar säkerhetskopior av databasen till blob-lagring. Du kan också använda den här tekniken manuellt. Mer information finns i [Använda Azure Storage för säkerhetskopiering och återställning av SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). En översikt över alla alternativ för säkerhetskopiering och återställning finns i [Säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

### Automatisera uppdateringar
Virtuella Azure-datorer kan använda [automatisk korrigering](virtual-machines-windows-sql-automated-patching.md) för att schemalägga en underhållsperiod för automatisk installation av viktiga Windows- och SQL Server-uppdateringar.

### CEIP (Customer Experience Improvement Program)
CEIP (Customer Experience Improvement Program) är aktiverat som standard. Det här är inte en hanteringsaktivitet såvida du inte vill inaktivera CEIP efter etableringen. Du kan anpassa eller inaktivera CEIP genom att ansluta till den virtuella datorn med Fjärrskrivbord. Kör sedan verktyget **Fel- och användningsrapportering för SQL Server**. Inaktivera rapporteringen genom att följa anvisningarna.

## Nästa steg
[Utforska utbildningsvägen](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) för SQL Server på virtuella datorer i Azure.

Har du fler frågor? Börja med att läsa [Vanliga frågor och svar om SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md). Du kan också lägga till dina frågor eller kommentarer längst ned i något av avsnitten om virtuella datorer med SQL och interagera med Microsoft och communityn.



<!--HONumber=Sep16_HO3-->


