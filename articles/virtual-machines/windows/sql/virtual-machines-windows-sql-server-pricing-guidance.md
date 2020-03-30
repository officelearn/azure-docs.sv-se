---
title: Prisguidning & hantera kostnader
description: Innehåller metodtips för att välja rätt SQL Server-prismodell för virtuella datorer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b7a8f7136d0899ab3afe04bce2d25bc2e7f8d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981718"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Prisvägledning för virtuella Azure SQL Server-datorer

Den här artikeln innehåller prisvägledning för [virtuella SQL Server-datorer](virtual-machines-windows-sql-server-iaas-overview.md) i Azure. Det finns flera alternativ som påverkar kostnaden, och det är viktigt att välja rätt bild som balanserar kostnader med affärskrav.

> [!TIP]
> Om du bara behöver ta reda på en kostnadsuppskattning för en specifik kombination av SQL Server-utgåva och storlek på virtuella datorer läser du prissidan för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Välj din plattform och SQL Server-utgåva i **OS/Software-listan.**
>
> ![Sidan Användargränssnitt på vm-prissidan](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Eller använd [priskalkylatorn](https://azure.microsoft.com/pricing/#explore-cost) för att lägga till och konfigurera en virtuell dator. 

## <a name="free-licensed-sql-server-editions"></a>Gratislicensierade SQL Server-utgåvor

Om du vill utveckla, testa eller bygga ett proof of concept använder du den fritt licensierade **SQL Server Developer-utgåvan**. Den här utgåvan har alla funktioner i SQL Server Enterprise-utgåvan, så att du kan skapa och testa alla typer av program. Du kan dock inte köra utvecklarutgåvan i produktion. En virtuell virtuell sql Server-utvecklarversion medför endast avgifter för kostnaden för den virtuella datorn, eftersom det inte finns några associerade SQL Server-licenskostnader.

Om du vill köra en lätt arbetsbelastning i produktion (<4 kärnor, <1 GB-minne <10 GB/databas) använder du den fritt licensierade **SQL Server Express-utgåvan**. En VIRTUELL SQL Server Express-utgåva medför också endast avgifter för kostnaden för den virtuella datorn.

För dessa utvecklings-/test- och lättviktsproduktionsarbetsbelastningar kan du också spara pengar genom att välja en mindre vm-storlek som matchar dessa arbetsbelastningar. DS1v2 kan vara ett bra val i vissa scenarier.

Information om hur du skapar en virtuell VIRTUELL SQL Server 2017-Azure med en av dessa avbildningar finns i följande länkar:

| Plattform | Fritt licensierade bilder |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Utvecklare Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Utvecklare Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Utvecklare Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Utvecklare Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Betalda SQL Server-utgåvor

Om du har en icke-lätt produktionsarbetsbelastning använder du någon av följande SQL Server-utgåvor:

| SQL Server Edition | Arbetsbelastning |
|-----|-----|
| Webb | Små webbplatser |
| Standard | Små till medelstora arbetsbelastningar |
| Enterprise | Stora eller verksamhetskritiska arbetsbelastningar|

Du har två alternativ att betala för SQL Server-licensiering för dessa utgåvor: *betala per användning* eller ta med din egen licens *(BYOL).*

## <a name="pay-per-usage"></a>Betala per användning

**Att betala SQL Server-licensen per användning** (kallas även **betala allt eftersom)** innebär att kostnaden per sekund för att köra Azure VM inkluderar kostnaden för SQL Server-licensen. Du kan se priserna för de olika SQL Server-utgåvorna (Web, Standard, Enterprise) på prissidan för Azure VM för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Kostnaden är densamma för alla versioner av SQL Server (2012 SP3 till 2017). Licenskostnaden per sekund beror på antalet virtuella processorer för virtuella datorer.

Att betala SQL Server-licensieringen per användning rekommenderas för:

- **Tillfälliga eller periodiska arbetsbelastningar**. Till exempel en app som behöver stödja ett evenemang för ett par månader varje år, eller affärsanalys på måndagar.

- **Arbetsbelastningar med okänd livslängd eller skala**. Till exempel en app som kanske inte krävs på några månader, eller som kan kräva mer eller mindre beräkningskraft, beroende på efterfrågan.

Information om hur du skapar en virtuell virtuell SQL Server 2017-azure med en av dessa pay-as-you-go-avbildningar finns i följande länkar:

| Plattform | Licensierade bilder |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Virtuell virtuell dator med SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Virtuell virtuell dator med SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Virtuell virtuell dator med SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Virtuell virtuell dator med SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Virtuell virtuell dator med SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Virtuell virtuell dator med SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Virtuell virtuell dator med SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Virtuell virtuell dator med SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> När du skapar en virtuell SQL Server-dator i portalen visar fönstret **Välj en storlek** en uppskattad kostnad. Det är viktigt att notera att den här uppskattningen endast är beräkningskostnaderna för att köra den virtuella datorn tillsammans med eventuella os-licenskostnader (Windows eller Linux-operativsystem från tredje part).
>
> ![Välj bladet VM-storlek](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Det innehåller inte ytterligare licenskostnader för SQL Server för webb-, standard- och enterprise-utgåvor. Om du vill få den mest exakta prisuppskattningen väljer du ditt operativsystem och SQL Server-utgåva på prissidan för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Det är nu möjligt att ändra licensieringsmodellen från pay-per-usage för att ta med egen licens (BYOL) och tillbaka. Mer information finns i avsnittet om att [ändra licensieringsmodellen för en virtuell SQL-dator](virtual-machines-windows-sql-ahb.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a>Ta med egen licens (BYOL)

**Att ta med din egen SQL Server-licens via License Mobility**, även kallad BYOL , innebär att använda en befintlig SQL Server Volume License med Software Assurance i en Virtuell Azure.Bringing your own SQL Server license through License Mobility , also referred to as **BYOL**, means using an existing SQL Server Volume License with Software Assurance in an Azure VM. En VIRTUELL SQL Server som använder BYOL debiterar endast kostnaden för att köra den virtuella datorn, inte för SQL Server-licensiering, med tanke på att du redan har förvärvat licenser och Software Assurance via ett volymlicensieringsprogram.

> [!IMPORTANT]
> BYOL-avbildningar kräver ett enterprise-avtal med Software Assurance. De är inte tillgängliga som en del av Azure Cloud Solution Partner (CSP) just nu. CSP-kunder kan ta med sin egen licens genom att distribuera en avbildning per användning och sedan aktivera [Azure Hybrid Benefit](virtual-machines-windows-sql-ahb.md).

> [!NOTE]
> BYOL-avbildningarna är för närvarande endast tillgängliga för virtuella Windows-datorer. Du kan dock manuellt installera SQL Server på en virtuell Linux-dator. Se riktlinjerna i [vanliga frågor och svar om Linux SQL VM](../../linux/sql/sql-server-linux-faq.md).

Att ta med din egen SQL-licensiering via License Mobility rekommenderas för:

- **Kontinuerliga arbetsbelastningar**. Till exempel en app som behöver stödja affärsverksamhet 24x7.

- **Arbetsbelastningar med känd livslängd och skala**. Till exempel en app som krävs för hela året och som efterfrågan har prognostiserats.

Om du vill använda BYOL med en VIRTUELL SQL Server måste du ha en licens för SQL Server Standard eller Enterprise and [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), vilket är ett obligatoriskt alternativ genom vissa volymlicensieringsprogram och ett valfritt köp med andra. Prisnivån som tillhandahålls via volymlicensieringsprogram varierar beroende på typ av avtal och kvantitet och eller åtagande till SQL Server. Men som en tumregel, att föra din egen licens för kontinuerlig produktion arbetsbelastningar har följande fördelar:

| BYOL-förmån | Beskrivning |
|-----|-----|
| **Kostnadsbesparingar** | [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) ger upp till 55 % besparingar. Mer information finns i [Växla licensieringsmodell](virtual-machines-windows-sql-ahb.md) |
| **Gratis passiv sekundär replik** | En annan fördel med att föra din egen licens är [gratis licensiering för en passiv sekundär replik](https://azure.microsoft.com/pricing/licensing-faq/) per SQL Server för hög tillgänglighet. Detta minskar hälften av licenskostnaden för en högtillgängliga SQL Server-distribution (till exempel med hjälp av Alltid på tillgänglighetsgrupper). Rättigheterna för att köra den passiva sekundära tillhandahålls via Fail-Over Servers Software Assurance-förmånen. |

Om du vill skapa en virtuell virtuell SQL Server 2017-Azure med en av dessa bring-your-own-licensavbildningar läser du de virtuella datorerna som är prefixerade med {BYOL}:

- [Virtuell virtuell dator med SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [Virtuell virtuell dator med SQL Server 2017 Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Meddela oss inom 10 dagar hur många SQL Server-licenser du använder i Azure. Länkarna till de tidigare bilderna har instruktioner om hur du gör detta.

> [!NOTE]
> Det är nu möjligt att ändra licensieringsmodellen från pay-per-usage för att ta med egen licens (BYOL) och tillbaka. Mer information finns i avsnittet om att [ändra licensieringsmodellen för en virtuell SQL-dator](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Minska kostnaderna

För att undvika onödiga kostnader, välj en optimal virtuell datorstorlek och överväga intermittenta avstängningar för icke-kontinuerliga arbetsbelastningar.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a>Storlek på datorn på rätt sätt

Licenskostnaden för SQL Server är direkt relaterad till antalet virtuella processorer. Välj en VM-storlek som matchar dina förväntade behov för CPU-, minnes-, lagrings- och I/O-bandbredd. En fullständig lista över alternativ för maskinstorlekar finns i Storlekarna för Windows VM och [Storlekar på virtuella datorer i Linux.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) [Windows VM sizes](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)

Det finns nya datorstorlekar som fungerar bra med vissa typer av SQL Server-arbetsbelastningar. Dessa datorers storlekar har hög minnes-, lagrings- och I/O-bandbredd, men de har ett lägre virtualiserat kärnantal. Tänk till exempel på följande exempel:

| Storlek på virtuell dator | Virtuella processorer | Minne | Max diskar | Max I/O-dataflöde | Kostnader för SQL-licensiering | Totala kostnader (Beräkning + licensiering) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 IOPS eller 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51 200 IOPS eller 768 MB/s | 75 % lägre | 57 % lägre |

> [!IMPORTANT]
> Detta är ett punkt-i-tid-exempel. De senaste specifikationerna finns i artiklarna för maskinstorlekar och Azure-prissidan för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) och [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

I föregående exempel kan du se att specifikationerna för **Standard_DS14v2** och **Standard_DS14-4v2** är identiska med undantag för virtuella processorer. Suffixet **-4v2** i slutet av **Standard_DS14-4v2** maskinstorlek anger antalet aktiva vCPUs. Eftersom SQL Server-licenskostnader är kopplade till antalet virtuella processorer minskar detta avsevärt kostnaden för den virtuella datorn i scenarier där de extra virtuella processorerna inte behövs. Det här är ett exempel och det finns många maskinstorlekar med begränsade vCPUs som identifieras med det här suffixmönstret. Mer information finns i blogginlägget [Presentera nya Azure VM-storlekar för mer kostnadseffektivt databasarbete](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Stäng av den virtuella datorn när det är möjligt

Om du använder arbetsbelastningar som inte körs kontinuerligt bör du överväga att stänga av den virtuella datorn under de inaktiva perioderna. Betala endast för det du använder.

Om du till exempel bara provar SQL Server på en Virtuell Azure-dator vill du inte debitera avgifter genom att av misstag låta den vara igång i veckor. En lösning är att använda den [automatiska avstängningsfunktionen](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Automatisk nedrullning av SQL VM](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatisk avstängning är en del av en större uppsättning liknande funktioner som tillhandahålls av [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

För andra arbetsflöden bör du överväga att automatiskt stänga av och starta om virtuella Azure-datorer med en skriptlösning, till exempel [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Att stänga av och frigöra den virtuella datorn är det enda sättet att undvika avgifter. Helt enkelt stoppa eller använda energialternativ för att stänga av den virtuella datorn fortfarande medför användningsavgifter.

## <a name="next-steps"></a>Nästa steg

Allmän Azure-prisvägledning finns i [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](../../../cost-management-billing/manage/getting-started.md). Den senaste prissättningen för virtuella datorer, inklusive SQL Server, finns på prissidan för Azure VM Azure för [virtuella datorer och](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) virtuella [Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

En översikt över SQL Server som körs på virtuella Azure-datorer finns i följande artiklar:

- [Översikt över SQL Server på virtuella Windows-datorer](virtual-machines-windows-sql-server-iaas-overview.md)
- [Översikt över SQL Server på virtuella Linux-datorer](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
