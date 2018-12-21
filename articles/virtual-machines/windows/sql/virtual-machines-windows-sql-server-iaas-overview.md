---
title: Översikt över SQL Server i Azure Windows Virtual Machines | Microsoft Docs
description: Lär dig hur du kör fullständiga versioner av SQL Server på Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2018
ms.author: jroth
ms.openlocfilehash: 416d1c7d45902735f58e397dd9195185ba0867df
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315255"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Vad är SQL Server på Azure Virtual Machines? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

Med [SQL Server på Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) kan du använda fullständiga versioner av SQL Server i molnet utan att behöva hantera lokal maskinvara. Virtuella SQL Server-datorer kan även förenkla licensieringskostnaderna när du betalar per användning.

Virtuella Azure-datorer körs i många olika [geografiska regioner](https://azure.microsoft.com/regions/) runtom i världen. De också erbjuder även en mängd olika [datorstorlekar](../sizes.md). I bildgalleriet för virtuella datorer kan du skapa en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Det här innebär att virtuella datorer är ett bra alternativ för många olika SQL Server-arbetsbelastningar.

## <a name="automated-updates"></a>Automatiserade uppdateringar

Virtuella Azure-datorer med SQL Server kan använda [automatisk korrigering](virtual-machines-windows-sql-automated-patching.md) för att schemalägga en underhållsperiod för automatisk installation av viktiga Windows- och SQL Server-uppdateringar.

## <a name="automated-backups"></a>Automatiserade säkerhetskopieringar

Virtuella Azure-datorer med SQL Server kan dra nytta av [automatisk säkerhetskopiering](virtual-machines-windows-sql-automated-backup-v2.md), som regelbundet skapar säkerhetskopior av databasen till Blob Storage. Du kan också använda den här tekniken manuellt. Mer information finns i [Använda Azure Storage för säkerhetskopiering och återställning av SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Hög tillgänglighet

Om du behöver hög tillgänglighet bör du överväga att konfigurera SQL Server-tillgänglighetsgrupper. Det här betyder att det finns flera virtuella Azure-datorer med SQL Server i ett virtuellt nätverk. Du kan konfigurera din lösning med hög tillgänglighet manuellt eller använda mallar i Azure Portal för automatisk konfiguration. En översikt över alternativ med hög tillgänglighet finns i [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Prestanda

Virtuella datorer i Azure erbjuder olika datorstorlekar för att uppfylla olika arbetsbelastningar. Virtuella SQL-datorer ger också automatisk lagringskonfiguration, som är optimerad för dina prestandakrav. Läs mer om hur du konfigurerar lagring för virtuella SQL-datorer i [Lagringskonfigurationen för virtuella SQL Server-datorer](virtual-machines-windows-sql-server-storage-configuration.md). För att finjustera prestanda, se [Prestandarelaterade metodtips för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Kom igång med virtuella SQL-datorer

För att komma igång väljer du en avbildning av en virtuell dator med SQL Server med rätt version, utgåva och operativsystem. Följande avsnitt innehåller direktlänkar till Azure Portal för galleriavbildningarna för virtuella datorer med SQL Server.

> [!TIP]
> Mer information om att förstå prissättningen för SOL-avbildningar kan du läsa [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer). 

### <a id="payasyougo"></a> Betala per användning
Följande tabell innehåller en matris med SQL Server-avbildningar som använder användningsbaserad betalning.

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Tillgängliga Linux SQL Server-VM-avbildningar finns i [Översikt över SQL Server på Azure Virtual Machines (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Det går nu att ändra så att licensieringsmodellen för en SQL Server VM som betalas per användning ska använda din egen licens. Mer information finns i avsnittet om att [ändra licensieringsmodellen för en virtuell SQL-dator](virtual-machines-windows-sql-ahb.md). 

### <a id="BYOL"></a> Bring your own license (BYOL)
Du kan även använda din egen licens (Bring your own license, BYOL). I det här scenariot betalar du bara för den virtuella datorn utan ytterligare avgifter för SQL Server-licensiering.  När du använder din egen licens kan du spara pengar långsiktigt för kontinuerliga arbetsbelastningar under produktion. Kraven för att använda det här alternativet finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md#byol) (Prisvägledning för virtuella SQL Server Azure-datorer).

Om du vill använda din egen licens kan du antingen konvertera en befintlig virtuell SQL-dator med betala per användning eller distribuera en avbildning med det prefigerade **{BYOL}**. Mer information om hur du växlar licensieringsmodellen mellan betala per användning och BYOL finns i avsnittet om att [ändra licensieringsmodellen för en virtuell SQL-dator](virtual-machines-windows-sql-ahb.md). 

| Version | Operativsystem | Utgåva |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |


### <a name="connect-to-the-vm"></a>Anslut till VM:en
När du har skapat din virtuella SQL Server-dator ansluter du till den från ett program eller ett verktyg som SQL Server Management Studio (SSMS). Anvisningar finns i [Anslut till en virtuell SQL Server-dator på Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrera dina data
Om du har en befintlig databas vill du antagligen flytta den till den nyetablerade virtuella SQL-datorn. En lista över migreringsalternativ och anvisningar finns i [Migrera en databas till SQL Server på en virtuell dator i Azure](virtual-machines-windows-migrate-sql.md).

## <a id="lifecycle"></a> Principuppdatering för SQL VM-avbildning
Azure underhåller en avbildning av en virtuell dator för varje kombination av operativsystem, version och utgåva som stöds. Det betyder att avbildningar uppdateras med tiden och att äldre avbildningar tas bort. Mer information finns i avsnittet **Avbildningar** i [Virtuella SQL Server-datorer, vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>CEIP (Customer Experience Improvement Program)
CEIP (Customer Experience Improvement Program) är aktiverat som standard. Det skickar periodvisa rapporter till Microsoft för att hjälpa att förbättra SQL Server. Det krävs inga hanteringsaktiviteter för CEIP om du inte vill inaktivera den efter etablering. Du kan anpassa eller inaktivera CEIP genom att ansluta till den virtuella datorn med Fjärrskrivbord. Kör sedan verktyget **Fel- och användningsrapportering för SQL Server**. Inaktivera rapporteringen genom att följa anvisningarna. Mer information om datainsamling finns i [SQL Server-sekretesspolicyn](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Relaterade produkter och tjänster
### <a name="windows-virtual-machines"></a>Virtuella Windows-datorer
* [Översikt över virtuella datorer](../overview.md)

### <a name="storage"></a>Storage
* [Introduktion till Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Nätverk
* [Översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md)
* [IP-adresser i Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Skapa ett fullständigt domännamn i Azure-portalen](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Dokumentation om SQL Server](https://docs.microsoft.com/sql/index)
* [Jämförelse med Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Nästa steg

Kom igång med SQL Server på Azure Virtual Machines:

* [Skapa en virtuell SQL Server-dator på Azure Portal](quickstart-sql-vm-create-portal.md)

Få svar på vanliga frågor om virtuella SQL-datorer:

* [Vanliga frågor och svar om SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)
