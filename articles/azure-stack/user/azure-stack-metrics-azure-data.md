---
title: Azure Monitor på Azure Stack | Microsoft Docs
description: Läs mer om Azure Monitor på Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2018
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: b8df87e5d5091219b9b44a6b957250e254c97fec
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341822"
---
# <a name="azure-monitor-on-azure-stack"></a>Azure Monitor på Azure Stack

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln innehåller en översikt över Azure Monitor-tjänsten i Azure Stack. Det diskuterar hur Azure Monitor och ytterligare information om hur du använder Azure Monitor på Azure Stack. 

En introduktion översikt och hur du kommer igång med Azure Monitor finns i globala Azure-artikeln [Kom igång med Azure Monitor på Azure Stack](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Azure Stack Monitor-bladet](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor är plattformstjänst som tillhandahåller en enda källa för övervakning av Azure-resurser. Med Azure Monitor kan du visualisera, fråga, vidarebefordra, arkivera och annars vidta åtgärder för mått och loggar från resurser i Azure. Du kan arbeta med dessa data med hjälp av Azure Stack-administrationsportalen, Monitor PowerShell Cmdlets, plattformsoberoende CLI eller Azure Monitor REST API: er. För den specifika anslutning som stöds av Azure Stack, se [använda övervakningsdata från Azure Stack](azure-stack-metrics-monitor.md)

> [!Note]  
Mått och diagnostikloggar är inte tillgängliga för Azure Stack Development Kit.

## <a name="prerequisites-for-azure-monitor-on-azure-stack"></a>Krav för Azure Monitor på Azure Stack

Registrera den **Microsoft.insights** resursprovidern på din prenumeration erbjudandet resursinställningar providers. Du kan kontrollera att resursprovidern är tillgänglig i erbjudandet som är associerade med din prenumeration:

1. Öppna administrationsportalen för Azure Stack.
2. Välj **erbjuder**.
3. Välja erbjudandet som är associerade med prenumerationen.
4. Välj **resursprovidrar** under **inställningar.** 
5. Hitta **Microsoft.Insights** i listan och kontrollera att statusen är **registrerad.**.

## <a name="overview-of-azure-monitor-on-azure-stack"></a>Översikt över Azure Monitor på Azure Stack

Som Azure Monitor på Azure ger Azure Monitor på Azure Stack beroende på infrastruktur-mått och loggar för de flesta tjänster.

## <a name="azure-monitor-sources-compute-subset"></a>Azure Monitor-källor: compute delmängd

![Azure Monitor på Azure Stack datakällor - beräkning delmängd](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Den **Microsoft.Compute** resursprovidern i Azure Stack innehåller:
 - Virtuella datorer 
 - Skalningsuppsättningar för virtuella datorer

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Program - diagnostikloggar, programloggar och mått

Program kan köras i Operativsystemet för en virtuell dator som körs med den **Microsoft.Compute** resursprovidern. Dessa program och virtuella datorer kan du generera en egen uppsättning loggar och mått. Azure Monitor är beroende av tillägget Azure diagnostics (Windows eller Linux) för att samla in de flesta måtten och loggarna på programnivå. 

Typer av åtgärder vara:
 - Prestandaräknare
 - Programloggar
 - Windows-händelseloggar
 - .NET-händelsekälla
 - IIS-loggar
 - Manifestbaserad ETW
 - Kraschdumpar
 - Kund-felloggar

> [!Note]  
> Linux-diagnostiktillägget på Azure Stack stöds inte.

### <a name="host-and-guest-vm-metrics"></a>Måtten värd- och gäst-VM

Ovanstående beräkningsresurser ha en dedikerad värd VM och gästoperativsystemet. Den Virtuella värddatorn och Gäst-OS är motsvarigheten till rotens virtuella dator och Gäst VM i Hyper-V hypervisor-program. Du kan samla in mått för både den Virtuella värddatorn och gästoperativsystemet. Du kan dessutom samla in diagnostikloggar för gästoperativsystemet. En lista över collectible mått för värd och Gäst VM-mått i Azure Stack finns på [stöds mått med Azure Monitor på Azure Stack](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Aktivitetslogg

Du kan söka aktivitetsloggar för information om dina beräkningsresurser som setts av Azure Stack-infrastruktur. Loggen innehåller information såsom tider när resurserna skapas eller förstörs. Aktivitetsloggar i Azure Stack är konsekvent med Azure. Mer information finns i beskrivningen av [översikten över aktivitet på Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Med Azure monitor källor: allt annat

![Azure Monitor på Azure Stack-källor – allt annat](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Resurser - mått och diagnostik för loggar

Collectible mått och diagnostik för loggar varierar beroende på resurstypen. En lista över collectible mått för varje resurs på Azure Stack är tillgänglig på mått som stöds. Mer information finns i [stöds mått med Azure Monitor på Azure Stack](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Aktivitetslogg

Aktivitetsloggen är detsamma för beräkningsresurser. 

### <a name="uses-for-monitoring-data"></a>Användningsområden för övervakning av data

**Store och arkivering**  

Vissa övervakningsdata är redan lagrade och är tillgängliga i Azure-Monitor under en tidsperiod. 
 - Mått lagras i 90 dagar. 
 - Aktivitetsloggposter som lagras i 90 dagar. 
 - Diagnostikloggar lagras inte.
 - Arkivera data till ett lagringskonto för längre kvarhållning.

**Fråga**  

Du kan använda Azure Monitor REST-API, plattformsoberoende kommandoradsgränssnittet (CLI) kommandon, PowerShell-cmdletar eller .NET SDK för att komma åt data i systemet eller Azure storage. 

**Visualisering**

Visualisera dina övervakningsdata i grafik och diagram och hitta trender snabbare än att leta genom informationen. 

Visualiseringsmetoderna omfattar:
 - Använd Azure Stack-användare och admin portal
 - Vidarebefordra data till Microsoft Power BI
 - Skicka data till tredje partsvisualiseringsverktyg med direktsänd strömning eller genom att läsa verktyget från ett arkiv i Azure-lagring

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Metoder för att komma åt Azure övervaka på Azure Stack

I allmänhet kan du ändra dataspårning, vidarebefordran och hämtning med någon av följande metoder. Inte alla metoder är tillgängliga för alla åtgärder eller datatyper.

 - [Azure Stack-portalen](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Plattformsoberoende kommandoraden Interface(CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST-API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Nästa steg

Mer information om alternativ för övervakning dataförbrukning på Azure Stack i artikeln [förbruka övervakningsdata från Azure Stack](azure-stack-metrics-monitor.md).
