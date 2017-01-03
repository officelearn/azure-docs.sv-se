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
ms.date: 12/01/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: d5169c25d09966ca72ee49ca995a8453ad764177
ms.openlocfilehash: 0e136d81ee3dbe57785d136755d3e800041aca1f


---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Översikt över SQL Server i Azure Virtual Machines
Det här ämnet beskriver dina alternativ när du kör en SQL Server på virtuella Azure-datorer (VM:ar), tillsammans med [länkar till portalavbildningar](#option-1-create-a-sql-vm-with-per-minute-licensing) samt en översikt över [vanliga uppgifter](#manage-your-sql-vm).

> [!NOTE]
> Om du redan är bekant med SQL Server och bara vill se hur man distribuerar en SQL Server-VM, hittar du mer information i [Etablera en virtuell dator med SQL Server i Azure Portal](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
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
Följande avsnitt innehåller direktlänkar till Azure Portal för galleriavbildningarna för virtuella datorer med SQL Server. Beroende på vilken avbildning du väljer kan du antingen betala för SQL Server-licensen per minut eller använda din egen licens (Bring your own license, BYOL).

Du hittar stegvisa anvisningar för den här processen i självstudien [Etablera en virtuell SQL Server-dator på Azure Portal](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Läs också [Prestandarelaterade metodtips för virtuella datorer med SQL Server](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), som förklarar hur du väljer lämplig datorstorlek och andra funktioner som är tillgängliga vid etablering.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Alternativ 1: Skapa en virtuell SQL-dator med licensiering per minut
Följande tabell innehåller en matris med de senaste SQL Server-avbildningarna i galleriet för virtuella datorer. Klicka på en länk om du vill skapa en ny virtuell dator med SQL med den version, den utgåva och det operativsystem du angett. 

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |

Förutom den här listan finns det andra kombinationer av SQL Server-versioner och operativsystem tillgängliga. Hitta andra avbildningar via en marketplace-sökning i Azure Portal. 

## <a name="a-idbyola-option-2-create-a-sql-vm-with-an-existing-license"></a><a id="BYOL"></a> Alternativ 2: Skapa en virtuell SQL-dator med en befintlig licens
Du kan även använda din egen licens (Bring your own license, BYOL). I det här scenariot betalar du bara för den virtuella datorn utan ytterligare avgifter för SQL Server-licensiering. Om du vill använda din egen licens använder du matrisen med de versioner, utgåvor och operativsystem för SQL Server som anges nedan. Namnen på avbildningarna föregås av **{BYOL}** i portalen.

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

Förutom den här listan finns det andra kombinationer av SQL Server-versioner och operativsystem tillgängliga. Hitta andra avbildningar via en marketplace-sökning i Azure Portal (Sök efter "{BYOL} SQL Server").

> [!IMPORTANT]
> För att kunna använda VM-avbildningar med BYOL måste du ha ett Enterprise-avtal med [licensmobilitet via Software Assurance i Azure](https://azure.microsoft.com/pricing/license-mobility/). Du måste också ha en giltig licens för den version/utgåva av SQL Server som du vill använda. Du måste [förse Microsoft med nödvändig BYOL-information](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) inom **tio** dagar från etableringen av den virtuella datorn.
> 

> [!NOTE]
> Det går inte att ändra så att licensieringsmodellen för en SQL Server VM som betalas per minut ska använda din egen licens. I så fall måste du skapa en ny virtuell dator med BYOL och migrera dina databaser till den nya virtuella datorn. 

## <a name="manage-your-sql-vm"></a>Hantera den virtuella SQL-datorn
Det finns flera valfria hanteringsuppgifter som du kan utföra när du har etablerat din virtuella SQL Server-dator. Till stor del konfigurerar du och hanterar SQL Server på exakt samma sätt som när du administrerar en lokal SQL Server-instans. Men vissa uppgifter är specifika för Azure. I följande avsnitt beskrivs några av dessa områden med länkar till mer information.

### <a name="connect-to-the-vm"></a>Anslut till VM:en
Ett av de mest grundläggande hanteringsstegen är att ansluta till din SQL Server-VM med hjälp av verktyg, som SQL Server Management Studio (SSMS). För instruktioner om hur du ansluter till din nya SQL Server-VM, kan du läsa [Anslut till en virtuell SQL Server-dator i Azure](virtual-machines-windows-sql-connect.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="migrate-your-data"></a>Migrera dina data
Om du har en befintlig databas vill du antagligen flytta den till den nyetablerade virtuella SQL-datorn. En lista över migreringsalternativ och anvisningar finns i [Migrera en databas till SQL Server på en virtuell dator i Azure](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="configure-high-availability"></a>Konfigurera hög tillgänglighet
Om du behöver hög tillgänglighet bör du överväga att konfigurera SQL Server-tillgänglighetsgrupper. Detta betyder att det finns flera virtuella Azure-datorer i ett virtuellt nätverk. På Azure Portal finns det en mall som ställer in den här konfigurationen åt dig. Mer information finns i [Konfigurera en AlwaysOn-tillgänglighetsgrupp för virtuella datorer i Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Om du vill konfigurera tillgänglighetsgruppen och associerade lyssnare manuellt läser du [Konfigurera AlwaysOn-tillgänglighetsgrupper för virtuella datorer i Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Mer information om hög tillgänglighet finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="back-up-your-data"></a>Säkerhetskopiera data
Virtuella Azure-datorer kan dra nytta av [automatisk säkerhetskopiering](virtual-machines-windows-sql-automated-backup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), som regelbundet skapar säkerhetskopior av databasen till Blob Storage. Du kan också använda den här tekniken manuellt. Mer information finns i [Använda Azure Storage för säkerhetskopiering och återställning av SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). En översikt över alla alternativ för säkerhetskopiering och återställning finns i [Säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="automate-updates"></a>Automatisera uppdateringar
Virtuella Azure-datorer kan använda [automatisk korrigering](virtual-machines-windows-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att schemalägga en underhållsperiod för automatisk installation av viktiga Windows- och SQL Server-uppdateringar.

### <a name="customer-experience-improvement-program-ceip"></a>CEIP (Customer Experience Improvement Program)
CEIP (Customer Experience Improvement Program) är aktiverat som standard. Det skickar periodvisa rapporter till Microsoft för att hjälpa att förbättra SQL Server. Det krävs inga hanteringsaktiviteter för CEIP om du inte vill inaktivera den efter etablering. Du kan anpassa eller inaktivera CEIP genom att ansluta till den virtuella datorn med Fjärrskrivbord. Kör sedan verktyget **Fel- och användningsrapportering för SQL Server**. Inaktivera rapporteringen genom att följa anvisningarna. 

Mer information finns i CEIP-avsnittet för ämnet [Acceptera licensvillkoren](https://msdn.microsoft.com/library/ms143343.aspx). 

## <a name="next-steps"></a>Nästa steg
[Utforska utbildningsvägen](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) för SQL Server på virtuella datorer i Azure.

För frågor om priser se [Prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Välj din mål-utgåva av SQL Server i listan **Operativsystem/programvara**. Visa därefter priser för virtuella datorer av olika storlek. 

Har du fler frågor? Börja med att läsa [Vanliga frågor och svar om SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Du kan också lägga till dina frågor eller kommentarer längst ned i något av avsnitten om virtuella datorer med SQL och interagera med Microsoft och communityn.




<!--HONumber=Jan17_HO1-->


