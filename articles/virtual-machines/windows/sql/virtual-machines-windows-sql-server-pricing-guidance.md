---
title: Hantera kostnader effektivt för SQL Server på Azure virtual machines | Microsoft Docs
description: Ta del av metodtips för att välja rätt SQL Server-dator-prismodell.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: jroth
ms.openlocfilehash: b19cf9cd4b67479d811a590cb80a618680f3b3d5
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496479"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Priser för SQL Server Azure-datorer

Den här artikeln innehåller prissättning riktlinjer för [SQL Server-datorer](virtual-machines-windows-sql-server-iaas-overview.md) i Azure. Det finns flera alternativ som påverkar kostnad och det är viktigt att välja vilken avbildning som balanserar kostnaderna med företagets behov.

> [!TIP]
> Om du endast vill ta reda på en kostnadsberäkning för en specifik kombination av SQL Server-versionen och VM-storlek kan se sidan med priser för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Välj din plattform och SQL Server-version från den **operativsystem/programvara** lista.
>
> ![Användargränssnittet på sidan med priser för virtuella datorer](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Eller Använd den [priskalkylator](https://azure.microsoft.com/pricing/#explore-cost) att lägga till och konfigurera en virtuell dator. 

## <a name="free-licensed-sql-server-editions"></a>Kostnadsfria licenser SQL Server-versioner

Om du vill utveckla, testa, eller skapa ett konceptbevis, använder du fritt licensierade **SQL Server Developer edition**. Den här versionen har alla funktioner i SQL Server Enterprise edition, så att du kan skapa och testa alla typer av program. Du kan dock köra Utvecklarversionen i produktion. En SQL Server Developer edition VM tillkommer endast kostnader för kostnaden för den virtuella datorn eftersom det finns inga associerade SQL Server-licenskostnaden.

Om du vill köra en enkel arbetsbelastning i produktion (< 4 kärnor, < 1 GB minne, < 10 GB-databas), Använd fritt licensierade **SQL Server Express edition**. En SQL Server Express edition VM medför också bara avgifter för kostnaden för den virtuella datorn.

Du kan också spara pengar genom att välja en mindre VM-storlek som matchar de här arbetsbelastningarna för dessa utveckling/test och förenklade produktionsarbetsbelastningar. DS1v2 kan vara ett bra val i vissa situationer.

Om du vill skapa en SQL Server 2017 Azure VM med någon av dessa avbildningar, se följande länkar:

| Plattform | Fritt licensierade avbildningar |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Betalversionerna av SQL Server

Om du har en icke-lightweight produktionsarbetsbelastning kan använda en av följande SQL Server-versioner:

| SQL Server-version | Arbetsbelastning |
|-----|-----|
| Webb | Små webbplatser |
| Standard | Små till medelstora arbetsbelastningar |
| Enterprise | Stora eller verksamhetskritiska arbetsbelastningar|

Du har två alternativ för att betala för SQL Server-licensiering för de här versionerna: *betala per användning* eller *använda din egen licens (BYOL)*.

## <a name="pay-per-usage"></a>Betala per användning

**Betala SQL Server-licens per användning** innebär att kostnaden per sekund för att köra Azure VM omfattar kostnaden för SQL Server-licens. Du kan se priserna för de olika SQL Server-versionerna (Web, Standard eller Enterprise) i Azure-VM prissidan för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Kostnaden är samma för alla versioner av SQL Server (2012 SP3 2017). Per sekund licenskostnaderna beror på antalet virtuella processorer i VM.

Betala SQL Server rekommenderas-licensiering per användning för:

- **Tillfälliga eller periodiska arbetsbelastningar**. Exempelvis kan en app som måste ha stöd för en händelse för några månader varje år eller Företagsanalys på måndagar.

- **Arbetsbelastningar med okänt livslängd eller skala**. Exempelvis kan en app som inte kan krävas i några månader eller som kan kräva mer eller mindre datorkraft, beroende på begäran.

Om du vill skapa en SQL Server 2017 Azure VM med en av avbildningarna betala per användning, se följande länkar:

| Plattform | Licensierade avbildningar |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> När du skapar en SQL Server-dator i portalen på **väljer du en storlek** visar en uppskattad kostnad. Det är viktigt att Observera att den här beräkningen är bara beräkningskostnaderna för att köra den virtuella datorn tillsammans med licenskostnaderna (Windows eller Linux-operativsystem från tredje part) för Operativsystemet.
>
> ![Välj bladet för VM-storlek](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Det inkluderar inte ytterligare SQL Server-licenskostnaden för Web, Standard eller Enterprise Edition. För att få den mest korrekta priser uppfattning, Välj ditt operativsystem och version av SQL Server på sidan med priser för [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) eller [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Nu är det möjligt att ändra så att licensieringsmodellen från betala per användning att använda din egen licens (BYOL) och sedan igen. Mer information finns i [ändra så att licensieringsmodellen för en SQL-VM](virtual-machines-windows-sql-ahb.md). 

## <a id="byol"></a> Använd din egen licens (BYOL)

**Ta din egen SQL Server-licens via License Mobility**, vilket även kallas **BYOL**, innebär att använda en befintlig volymlicens för SQL Server med Software Assurance i en Azure VM. En SQL Server VM med BYOL endast avgifter för kostnaden för att köra den virtuella datorn, inte för SQL Server-licensiering, med hänsyn till att du har redan har köpt licenser med Software Assurance via volymlicensiering program.

> [!IMPORTANT]
> BYOL-avbildningar kräver ett Enterprise-avtal med Software Assurance. De är inte tillgängliga som en del av Azure Cloud Solution Partner (CSP) just nu.

> [!NOTE]
> BYOL-avbildningar är för närvarande endast tillgängliga för Windows-datorer. Du kan manuellt installera SQL Server på en endast Linux virtuell dator. Finns i riktlinjerna i den [Linux SQL VM vanliga frågor och svar](../../linux/sql/sql-server-linux-faq.md).

Ta din egen SQL rekommenderas licensiering via License Mobility för:

- **Kontinuerlig arbetsbelastningar**. Exempelvis kan en app som måste ha stöd för verksamheten 24 x 7.

- **Arbetsbelastningar med kända livstid och skala**. Exempelvis kan en app som krävs för hela året och har varit prognostiserat vilka begäran.

Om du vill använda BYOL med en SQL Server-dator, måste du ha en licens för SQL Server Standard eller Enterprise och [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), vilket är ett alternativ som krävs för vissa volymlicensieringsprogram och ett valfritt köp med andra. Den prisnivå som tillhandahålls via volymlicensieringsprogram varierar beroende på vilken typ av avtal och kvantitet och eller åtagande till SQL Server. Men som en tumregel tar med din egen licens för kontinuerlig produktionsarbetsbelastningar har följande fördelar:

| BYOL-förmånen | Beskrivning |
|-----|-----|
| **Kostnadsbesparingar** | Ta din egen SQL Server-licens är mer kostnadseffektivt än att betala per användning om en arbetsbelastning som körs kontinuerligt SQL Server Standard eller Enterprise för *mer än 10 månader*. |
| **Långsiktiga besparingar** | I genomsnitt är det *30% billigare per år* att köpa eller förnyar en SQL Server-licens för de första 3 år. Dessutom efter 3 år behöver du inte längre förnya licensen, bara betala för Software Assurance. I det här läget är det *200% billigare*. |
| **Kostnadsfri passiv sekundär replik** | En annan fördel för att göra din egen licens är de [kostnadsfria licenser för en passiv sekundär replik](https://azure.microsoft.com/pricing/licensing-faq/) per SQL Server för hög tillgänglighet. Detta minskar i halva licensieringskostnader för en högtillgänglig SQL Server-distribution (t.ex, med hjälp av Always On-Tillgänglighetsgrupper). Behörighet för att köra passiv sekundär tillhandahålls via redundans servrar Software Assurance-förmånen. |

Se de virtuella datorerna som föregås av prefixet ”{BYOL}” för att skapa en SQL Server 2017 Azure VM med någon av dessa avbildningar med bring-your-own-license:

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Berätta för oss inom 10 dagar hur många SQL Server-licenser som du använder i Azure. Länkar till de föregående bilderna har anvisningar om hur du gör detta.

> [!NOTE]
> Nu är det möjligt att ändra så att licensieringsmodellen från betala per användning att använda din egen licens (BYOL) och sedan igen. Mer information finns i [ändra så att licensieringsmodellen för en SQL-VM](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Minska kostnaderna

Välj en optimal VM-storlek för att undvika onödiga kostnader och Överväg att tillfälligt avstängningar för icke-kontinuerliga arbetsbelastningar.

### <a id="machinesize"></a> Rätt storlek på den virtuella datorn

Licensieringskostnader för SQL Server är direkt relaterade till antalet virtuella processorer. Välj en VM-storlek som motsvarar din förväntade behov för processor, minne, lagring och i/o-bandbredd. En fullständig lista över storleksalternativ för datorer, se [Windows VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) och [Linux VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Det finns nya storlekar för datorer som fungerar bra med vissa typer av SQL Server-arbetsbelastningar. Dessa datorer storlekar upprätthålla hög minne, lagring och i/o-bandbredd, men de har ett lägre antal virtuella kärnor. Titta exempelvis i följande exempel:

| Storlek på virtuell dator | vCPU:er | Minne | Maximalt antal diskar | Maximalt genomflöde för i/o | Licensieringskostnaderna för SQL | Totala kostnader (beräkning + licensing) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS eller 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51,200 IOPS eller 768 MB/s | 75% lägre | 57% lägre |

> [!IMPORTANT]
> Detta är en point-in-time-exempel. Senaste-specifikationerna finns till datorn storlekar artiklar och priser för Azure [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) och [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

I exemplet ovan kan du se att specifikationerna för **Standard_DS14v2** och **Standard_DS14 4v2** är identiska förutom virtuella processorer. Suffixet **-4v2** i slutet av den **Standard_DS14 4v2** datorstorlek anger antalet aktiva virtuella processorer. Eftersom SQL Server-licenskostnader är knutna till antalet virtuella processorer, minskar vilket avsevärt kostnaden för den virtuella datorn i scenarier där de extra virtuella processorer inte behövs. Det här är ett exempel och det finns många storlekar för datorer med begränsade vcpu: er som identifieras med det här mönstret suffix. Mer information finns i bloggposten [presenterar nya Azure VM-storlekar för mer kostnadseffektiv databasen arbete](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Stäng av den virtuella datorn när det är möjligt

Om du använder alla arbetsbelastningar som inte kör kontinuerligt, Överväg att stänga av den virtuella datorn under inaktiva perioder. Betala endast för det du använder.

Om du försöker bara ut SQL Server på en Azure VM, skulle du till exempel inte vill kan medföra ytterligare kostnader genom att lämna den körs i veckor av misstag. En lösning är att använda den [funktionen för automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![SQL VM autoshutdown](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatisk avstängning är en del av en större uppsättning liknande funktioner som tillhandahålls av [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Överväg att automatiskt stänga av och starta om Azure virtuella datorer med en lösning för skript som för andra arbetsflöden [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Stänger av och frigörs den virtuella datorn är det enda sättet att undvika avgifter. Helt enkelt stoppas eller med energialternativen för att stänga av den virtuella datorn fortfarande tillkommer avgifter för användning.

## <a name="next-steps"></a>Nästa steg

För allmänna Azure priser vägledning finns i [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](../../../billing/billing-getting-started.md). De senaste virtuella datorerna prissättningen, inklusive SQL Server, finns i Azure VM-Azure prissidan för [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) och [virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

En översikt över SQL Server som körs på Azure Virtual Machines, finns i följande artiklar:

- [Översikt över SQLServer på virtuella Windows-datorer](virtual-machines-windows-sql-server-iaas-overview.md)
- [Översikt över SQLServer på virtuella Linux-datorer](../../linux/sql/sql-server-linux-virtual-machines-overview.md)