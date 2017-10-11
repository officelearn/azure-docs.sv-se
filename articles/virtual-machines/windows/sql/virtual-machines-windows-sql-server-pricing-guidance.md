---
title: "Hantera kostnaderna effektivt för SQL Server på Azure virtual machines | Microsoft Docs"
description: "Innehåller metodtips för att välja rätt SQL Server-virtuella prismodell."
services: virtual-machines-windows
documentationcenter: na
author: luisherring
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/18/2017
ms.author: jroth
ms.openlocfilehash: 29a92f0c70bffedeb75c50b7fc3b687ee5ee227d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Priser för SQL Server Azure virtuella datorer

Det här avsnittet beskrivs priser för SQL Server-datorer i Azure. Det finns flera alternativ som påverkar kostnad och det är viktigt att välja rätt avbildningen som balanserar kostnader med företagets krav.

## <a name="free-licensed-sql-server-editions"></a>Kostnadsfri-licensierade versioner av SQL Server

Om du vill utveckla, testa eller skapa ett konceptbevis sedan använda den kostnadsfria-licensierade **SQL Server Developer edition**. Den här versionen har allt i SQL Server Enterprise edition, vilket du kan använda den för att skapa det program som du vill. Det har inte kunna köras i produktionen. En SQL Server Developer VM debiterar endast för kostnaden för den virtuella datorn, inte för SQL Server-licensiering.

Om du vill köra en lätt arbetsbelastning i produktion (< 4 kärnor < 1 GB minne, < 10 GB-databaser), sedan använda den kostnadsfria-licensierade **SQL Server Express edition**. SQL Express VM debiterar endast för kostnaden för den virtuella datorn inte SQL-licensiering.

Du kan också spara pengar genom att välja en mindre VM-storlek som matchar dessa arbetsbelastningar för dessa utveckling och testning eller lightweight produktionsarbetsbelastningar. DS1v2 kan vara ett bra alternativ för dessa arbetsbelastningar.

Om du vill skapa en SQL Server 2016 virtuella Azure-datorn med någon av dessa avbildningar, se följande länkar:

- [SQL Server 2016 Developer Azure VM](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016-ARM)
- [SQL Server 2016-Express Azure VM](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016-ARM)

## <a name="paid-sql-server-editions"></a>Betald SQL Server-utgåvorna

Om du har en icke-lightweight produktion arbetsbelastning kan använda något av följande SQL Server-versioner:

| SQL Server-version | Arbetsbelastning |
|-----|-----|
| Webb | Liten webbplatser |
| Standard | Små till medelstora arbetsbelastningar |
| Enterprise | Stora eller verksamhetskritiska arbetsbelastningar|

Du har två alternativ för att betala för SQL Server-licensiering för dessa versioner: *betala per användning* eller *ta med din egen licens*.

### <a name="pay-per-usage"></a>Betala per användning

**Betala per användning i SQL Server-licens** innebär att kostnaden per minut för att köra Azure VM omfattar kostnaden för SQL Server-licens. Du kan se priser för de olika SQL Server-versionerna (Web, Standard, Enterprise) i den [Azure VM sida med priser](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). Kostnaden är samma för alla versioner av SQL Server (2008 R2 2016). Precis som med SQL Server-licensiering i allmänhet beror licensiering kostnaden per minut på antal kärnor VM.

Betala SQL Server rekommenderas-licensiering per användning för:

- Tillfällig eller periodiska arbetsbelastningar. Till exempel en app som behöver stöd för en händelse för ett antal månader varje år eller Företagsanalys på måndagen.
- Arbetsbelastningar med okänt livslängd eller skala. Till exempel en app som inte kan krävas i några månader eller som kan kräva mer eller mindre datorkraft, beroende på begäran.

Om du vill skapa en SQL Server 2016 virtuella Azure-datorn med någon av dessa betala per användning avbildningar, se följande länkar:

- [SQL Server 2016 Web Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)
- [SQL Server 2016 Standard-Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)
- [SQL Server 2016 Enterprise Azure VM](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)

> [!IMPORTANT]
> När du skapar en virtuell dator med SQL Server i Azure portal den beräknade kostnaden för varje månad visas på den **välja en storlek** bladet innehåller inte SQL Server-licensieringskostnaderna. Detta är endast kostnaden för den virtuella datorn.
>
> ![Välj bladet för VM-storlek](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Detta är den totala uppskattade kostnaden för de kostnadsfria-licensierade Express och utvecklare utgåvorna av SQL Server. Men för webb-, Standard- och Enterprise hittar du ytterligare SQL licensieringskostnader på den [sida med priser virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). På sidan prisnivå väljer du ditt mål-utgåva av SQL Server.

### <a name="bring-your-own-license-byol"></a>Bring-your-own-license (BYOL)

**Ta din egen SQL Server-licens genom Licensmobilitet**, vilket även kallas **BYOL**, sätt med hjälp av en befintlig SQL Server-volymlicens med Software Assurance i en Azure VM. En SQL Server-VM med BYOL debiterar endast för kostnaden för att köra den virtuella datorn, inte för SQL Server-licensiering med tanke på att du redan har köpt licenser och Software Assurance via Volume Licensing-program.

Ta din egen SQL rekommenderas-licensiering via licensera Mobility för:

- Kontinuerlig arbetsbelastningar. Till exempel en app som behöver stöd för verksamheten 24 x 7.
- Arbetsbelastningar med kända livstid och skala. Till exempel en app som krävs för hela året och vilka begäran har tagits prognostiserat.

Om du vill använda BYOL med en SQL Server-VM, måste du ha en licens för SQL Server Standard eller Enterprise och [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), vilket är ett alternativ som krävs för igenom några [volymlicensiering](https://www.microsoft.com/en-us/download/details.aspx?id=10585) program och en valfri inköp med andra.  Prisnivåer tillhandahålls genom volymlicensieringsprogram varierar beroende på vilken typ av avtalet och kvantitet och eller åtagande till SQL Server. Men som en tumregel ta fram din egen licens för kontinuerlig produktionsarbetsbelastningar har följande fördelar:

| BYOL förmån | Beskrivning |
|-----|-----|
| **Besparingar** | Ta din egen SQL Server-licens är mer kostnadseffektivt än betala per användning om en arbetsbelastning körs alltid SQL Server Standard eller Enterprise för *mer än 10 månader*. |
| **Långsiktig besparingar** | Det är i genomsnitt *30% billigare per år* att köpa eller förnya en SQL Server-licens för de första 3 år. Dessutom efter 3 år behöver du inte längre förnya licensen, betalar bara för Software Assurance. Då är det *200% billigare*. |
| **Ledigt passiva sekundär replik** | En annan fördel med att din egen licens är de [ledigt licensiering för en sekundär replik för passiva](https://azure.microsoft.com/pricing/licensing-faq/) per SQL Server för hög tillgänglighet. Detta minskar i hälften av licensieringen kostnaden för en högtillgänglig SQL Server-distribution (t.ex. med Always On-Tillgänglighetsgrupper). Behörighet för att köra den passiva sekundärt tillhandahålls via failover servrar Software Assurance-förmånen. |

Om du vill skapa en SQL Server 2016 virtuella Azure-datorn med någon av dessa bring-your-äger-licens avbildningar finns i de virtuella datorerna prefixet ”{BYOL}”:

- [SQL Server 2016 Enterprise Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [SQL Server 2016 Standard-Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Meddela oss inom 10 dagar hur många SQL Server-licenser som du ska använda i Azure. Länkar till föregående bilder har instruktioner om hur du gör detta.

## <a name="avoid-unecessary-costs"></a>Undvika onödiga kostnader

Om du använder alla arbetsbelastningar som inte kör kontinuerligt, bör du stänga av den virtuella datorn under inaktiva perioder. Betala endast för det du använder.

Till exempel om du bara i SQL Server på en Azure VM, vill du inte avgifter genom att låta den kör för veckor av misstag. En lösning är att använda den [automatisk avstängning](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![SQL-VM autoshutdown](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatisk avstängning är en del av en större uppsättning liknande funktioner som tillhandahålls av [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Överväg att automatiskt stänger och startar om virtuella Azure-datorer med en skript-lösning som för andra arbetsflöden [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Stänga av och det har frigjorts din virtuella dator är det enda sättet att undvika kostnader. Bara stoppas eller med Energialternativ för att stänga av den virtuella datorn fortfarande debiteras användning.

## <a name="next-steps"></a>Nästa steg

För allmän Azure priser vägledning, se [förhindrar oväntade kostnader med Azure fakturerings- och kostnaden management](../../../billing/billing-getting-started.md).

Senaste virtuella datorer prissättning, inklusive SQL Server, finns det [Azure VM sida med priser](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard).

Granska andra virtuell dator med SQL Server-ämnen på [SQL Server på Azure virtuella datorer – översikt](virtual-machines-windows-sql-server-iaas-overview.md).
