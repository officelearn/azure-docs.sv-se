---
title: "Översikt över SQL Server i Azure Virtual Machines | Microsoft Docs"
description: "Läs mer om hur du kör fullständiga SQL Server-utgåvor på virtuella datorer med Azure. Hämta direktlänkar till alla SQL Server VM-avbildningar och tillhörande innehåll."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fcc2883ae52bb3d4be098ce741bbf5f5047777d2


---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Översikt över SQL Server i Azure Virtual Machines
Det här ämnet beskriver dina alternativ när du kör en SQL Server på virtuella Azure-datorer (VM:ar), tillsammans med [länkar till portalavbildningar](#option-1-create-a-sql-vm-with-per-minute-licensing) samt en översikt över [vanliga uppgifter](#manage-your-sql-vm).

> [!NOTE]
> Om du redan är bekant med SQL Server och bara vill se hur man distribuerar en SQL Server-VM, hittar du mer information i [Etablera en virtuell dator med SQL Server i Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).
> 
> 

## <a name="overview"></a>Översikt
Om du är en databasadministratör eller en utvecklare, kan Azure VM:ar ge dig ett sätt att flytta dina lokala SQL Server-arbetsbelastningar och program till molnet. Följande videoklipp ger en teknisk översikt över Azure VM:ar med SQL Server.

> [!VIDEO https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016/player]
> 
> 

Videoklippet går igenom följande områden:

| Tid | Område |
| --- | --- |
| 00:21 |Vad är Azure VM:ar? |
| 01:45 |Säkerhet |
| 02:50 |Anslutning |
| 03:30 |Lagringstillförlitlighet och prestanda |
| 05:20 |VM-storlekar |
| 05:54 |Hög tillgänglighet och SLA |
| 07:30 |Konfigurationssupport |
| 08:00 |Övervakning |
| 08:32 |Demo: Skapa en SQL Server 2016 VM |

> [!NOTE]
> Videoklippet fokuserar på SQL Server 2016 men Azure erbjuder VM-avbildningar för flera versioner av SQL Server inklusive 2008, 2012, 2014 och 2016. 
> 
> 

## <a name="scenarios"></a>Scenarier
Det finns flera skäl att välja att ha sin data i Azure. Om ditt program flyttar till Azure, ger det bättre prestanda om man även flyttar dess data. Men det finns fler fördelar. Du får automatiskt tillgång till flera datacenter för en global närvaro och haveriberedskap. Din data är också mycket säker och beständig.

SQL Server som kör på Azure VM:ar är ett alternativ för att lagra din relationsdata i Azure. Det är bra för flera scenarier. Du kanske till exempel vill konfigurera din Azure VM så att den är så lik en lokal SQL Server-dator som möjligt. Eller så kanske du vill köra ytterligare program och tjänster på samma databasserver. Det finns två huvudsakliga resurser som hjälper dig att tänka igenom ännu fler scenarier och överväganden:

* [SQL Server på virtuella Azure-datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/) ger en översikt över de bästa scenarierna för att använda SQL Server på virtuella Azure-datorer. 
* [Välj ett molnbaserat SQL Server-alternativ: Azure SQL (PaaS) Database eller SQL Server på Azure VM:ar (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) ger dig en detaljerad jämförelse mellan SQL Database och SQL Server som körs på en VM.

## <a name="create-a-new-sql-vm"></a>Skapa en ny virtuell SQL-dator
Följande avsnitt innehåller direktlänkar till Azure-portalen för galleriavbildningarna för virtuella datorer med SQL Server. Beroende på vilken avbildning du väljer kan du antingen betala för SQL Server-licensen per minut eller använda din egen licens (Bring your own license, BYOL).

Du hittar stegvisa anvisningar för den här processen i självstudien [Etablera en virtuell SQL Server-dator på Azure Portal](virtual-machines-windows-portal-sql-server-provision.md). Läs också [Prestandarelaterade metodtips för virtuella datorer med SQL Server](virtual-machines-windows-sql-performance.md), som förklarar hur du väljer lämplig datorstorlek och andra funktioner som är tillgängliga vid etablering.

## <a name="option-1-create-a-sql-vm-with-perminute-licensing"></a>Alternativ 1: Skapa en virtuell SQL-dator med licensiering per minut
Följande tabell innehåller en matris med tillgängliga SQL Server-avbildningar i galleriet för virtuella datorer. Klicka på en länk om du vill skapa en ny virtuell dator med SQL med den version, den utgåva och det operativsystem du angett.

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL 2016** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2) |
| **SQL 2014 SP1** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2) |
| **SQL 2014** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2) |
| **SQL 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012) |
| **SQL 2008 R2 SP3** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |
| **SQL 2008 R2 SP3** |Windows Server 2012 |[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012) |

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Alternativ 2: Skapa en virtuell SQL-dator med en befintlig licens
Du kan även använda din egen licens (Bring your own license, BYOL). I det här scenariot betalar du bara för den virtuella datorn utan ytterligare avgifter för SQL Server-licensiering. Om du vill använda din egen licens använder du matrisen med de versioner, utgåvor och operativsystem för SQL Server som anges nedan. Namnen på avbildningarna föregås av **{BYOL}** i portalen.

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2016** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2) |
| **SQL Server 2014 SP1** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

> [!IMPORTANT]
> För att kunna använda VM-avbildningar med BYOL måste du ha ett Enterprise-avtal med [licensmobilitet via Software Assurance i Azure](https://azure.microsoft.com/pricing/license-mobility/). Du måste också ha en giltig licens för den version/utgåva av SQL Server som du vill använda. Du måste [förse Microsoft med nödvändig BYOL-information](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) inom **tio** dagar från etableringen av den virtuella datorn.
> 
> 

## <a name="manage-your-sql-vm"></a>Hantera den virtuella SQL-datorn
Det finns flera valfria hanteringsuppgifter som du kan utföra när du har etablerat din virtuella SQL Server-dator. Till stor del konfigurerar du och hanterar SQL Server på exakt samma sätt som när du administrerar en lokal SQL Server-instans. Men vissa uppgifter är specifika för Azure. I följande avsnitt beskrivs några av dessa områden med länkar till mer information.

### <a name="connect-to-the-vm"></a>Anslut till VM:en
Ett av de mest grundläggande hanteringsstegen är att ansluta till din SQL Server-VM med hjälp av verktyg, som SQL Server Management Studio (SSMS). För instruktioner om hur du ansluter till din nya SQL Server-VM, kan du läsa [Anslut till en virtuell SQL Server-dator i Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrera dina data
Om du har en befintlig databas vill du antagligen flytta den till den nyetablerade virtuella SQL-datorn. En lista över migreringsalternativ och anvisningar finns i [Migrera en databas till SQL Server på en virtuell dator i Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Konfigurera hög tillgänglighet
Om du behöver hög tillgänglighet bör du överväga att konfigurera SQL Server-tillgänglighetsgrupper. Detta betyder att det finns flera virtuella Azure-datorer i ett virtuellt nätverk. På Azure Portal finns det en mall som ställer in den här konfigurationen åt dig. Mer information finns i [Konfigurera en AlwaysOn-tillgänglighetsgrupp för virtuella datorer i Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Om du vill konfigurera tillgänglighetsgruppen och associerade lyssnare manuellt läser du [Konfigurera AlwaysOn-tillgänglighetsgrupper för virtuella datorer i Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Mer information om hög tillgänglighet finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Säkerhetskopiera data
Virtuella Azure-datorer kan dra nytta av [automatisk säkerhetskopiering](virtual-machines-windows-sql-automated-backup.md), som regelbundet skapar säkerhetskopior av databasen till blob-lagring. Du kan också använda den här tekniken manuellt. Mer information finns i [Använda Azure Storage för säkerhetskopiering och återställning av SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). En översikt över alla alternativ för säkerhetskopiering och återställning finns i [Säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatisera uppdateringar
Virtuella Azure-datorer kan använda [automatisk korrigering](virtual-machines-windows-sql-automated-patching.md) för att schemalägga en underhållsperiod för automatisk installation av viktiga Windows- och SQL Server-uppdateringar.

### <a name="customer-experience-improvement-program-ceip"></a>CEIP (Customer Experience Improvement Program)
CEIP (Customer Experience Improvement Program) är aktiverat som standard. Det skickar periodvisa rapporter till Microsoft för att hjälpa att förbättra SQL Server. Det krävs inga hanteringsaktiviteter för CEIP om du inte vill inaktivera den efter etablering. Du kan anpassa eller inaktivera CEIP genom att ansluta till den virtuella datorn med Fjärrskrivbord. Kör sedan verktyget **Fel- och användningsrapportering för SQL Server**. Inaktivera rapporteringen genom att följa anvisningarna. 

Mer information finns i CEIP-avsnittet för ämnet [Acceptera licensvillkoren](https://msdn.microsoft.com/library/ms143343.aspx). 

## <a name="next-steps"></a>Nästa steg
[Utforska utbildningsvägen](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) för SQL Server på virtuella datorer i Azure.

Har du fler frågor? Börja med att läsa [Vanliga frågor och svar om SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md). Du kan också lägga till dina frågor eller kommentarer längst ned i något av avsnitten om virtuella datorer med SQL och interagera med Microsoft och communityn.




<!--HONumber=Nov16_HO2-->


