---
title: Översikt över automatisk skalning med VM-skalningsuppsättningar i Azure
description: Lär dig mer om de olika sätt som du automatiskt kan skala en Azure-skala för virtuell dator baserat på prestanda eller på ett fast schema
author: cynthn
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb96be187502afcccfd3fb2c88f709facfbc3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278149"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Översikt över automatisk skalning med VM-skalningsuppsättningar i Azure
En Azure-skalningsuppsättning för virtuella datorer kan automatiskt öka eller minska antalet VM-instanser som kör ditt program. Detta automatiserade och elastiska beteende minskar hanteringen för att övervaka och optimera programmets prestanda. Du skapar regler som definierar den godtagbara prestandan för en positiv kundupplevelse. När de definierade tröskelvärdena uppfylls vidtar regler för automatisk skalning för att justera kapaciteten för din skalningsuppsättning. Du kan också schemalägga händelser så att de automatiskt ökar eller minskar kapaciteten för din skala som ställts in vid fasta tider. Den här artikeln innehåller en översikt över vilka prestandamått som är tillgängliga och vilka åtgärder automatisk skalning kan utföra.


## <a name="benefits-of-autoscale"></a>Fördelar med automatisk skalning
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen.

När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du styr vilka mått som ska övervakas, till exempel CPU eller minne, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många VM-instanser som ska läggas till i skalningsuppsättningen.

På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.


## <a name="use-host-based-metrics"></a>Använda värdbaserade mått
Du kan skapa regler för automatisk skalning som inbyggda värdmått som är tillgängliga från dina VM-instanser. Värdmått ger dig insyn i prestanda för VM-instanser i en skalningsuppsättning utan att behöva installera eller konfigurera ytterligare agenter och datainsamlingar. Regler för automatisk skalning som använder dessa mått kan skalas ut eller i antalet VM-instanser som svar på CPU-användning, minnesbehov eller diskåtkomst.

Regler för automatisk skalning som använder värdbaserade mått kan skapas med något av följande verktyg:

- [Azure-portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure-mall](tutorial-autoscale-template.md)

Om du vill skapa regler för automatisk skalning som använder mer detaljerade prestandamått kan du [installera och konfigurera Azure diagnostics-tillägget](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) på VM-instanser eller konfigurera programmet använda App [Insights](#application-level-metrics-with-app-insights).

Regler för automatisk skalning som använder värdbaserade mått, in-guest VM-mått med Azure-diagnostiktillägget och App Insights kan använda följande konfigurationsinställningar.

### <a name="metric-sources"></a>Metriska källor
Regler för automatisk skalning kan använda mått från någon av följande källor:

| Måttkälla        | Användningsfall                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuell skaluppsättning    | För värdbaserade mått som inte kräver att ytterligare agenter installeras eller konfigureras.                                  |
| Lagringskonto      | Azure-diagnostiktillägget skriver prestandamått till Azure-lagring som sedan förbrukas för att utlösa regler för automatisk skalning. |
| Service Bus-kö    | Ditt program eller andra komponenter kan överföra meddelanden i en Azure Service Bus-kö för att utlösa regler.                   |
| Application Insights | Ett instrumenteringspaket som är installerat i ditt program som strömmar mått direkt från appen.                         |


### <a name="autoscale-rule-criteria"></a>Regelvillkor för automatisk skalning
Följande värdbaserade mått är tillgängliga för användning när du skapar regler för automatisk skalning. Om du använder Azure diagnostic extension eller App Insights definierar du vilka mått som ska övervakas och användas med regler för automatisk skalning.

| Måttnamn               |
|---------------------------|
| Procent CPU            |
| Nätverk – inkommande                |
| Nätverk – utgående               |
| Diskläs byte           |
| Byte av diskskrivning          |
| Diskläsningsåtgärder/sek  |
| Diskskrivningsåtgärder/sek |
| Återstående CPU-krediter     |
| Cpu-krediter förbrukas      |

När du skapar regler för automatisk skalning för att övervaka ett visst mått tittar reglerna på en av följande aggregeringsåtgärder för mått:

| Sammansättningstyp |
|------------------|
| Medel          |
| Minimum          |
| Maximal          |
| Totalt            |
| Sista             |
| Antal            |

Reglerna för automatisk skalning utlöses sedan när måtten jämförs med den definierade tröskelvärdet med någon av följande operatorer:

| Operator                 |
|--------------------------|
| Större än             |
| Större än eller lika med |
| Mindre än                |
| Mindre än eller lika med    |
| Lika med                 |
| Inte lika med             |


### <a name="actions-when-rules-trigger"></a>Åtgärder när regler utlöses
När en regel för automatisk skalning utlöses kan skalningsuppsättningen automatiskt skalas på något av följande sätt:

| Skala åtgärd     | Användningsfall                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Öka antalet med   | Ett fast antal VM-instanser att skapa. Användbart i skaluppsättningar med ett mindre antal virtuella datorer.                                           |
| Öka procenten med | En procentbaserad ökning av VM-instanser. Bra för större skalsatser där en fast ökning kanske inte märkbart förbättrar prestanda. |
| Öka antalet till   | Skapa så många VM-instanser som krävs för att nå ett önskat maximalt belopp.                                                            |
| Minska antalet med   | Ett fast antal VM-instanser att ta bort. Användbart i skaluppsättningar med ett mindre antal virtuella datorer.                                           |
| Minska procent med | En procentbaserad minskning av VM-instanser. Bra för större skalsatser där en fast ökning kanske inte märkbart minskar resursförbrukningen och kostnaderna. |
| Minska antalet till   | Ta bort så många VM-instanser som krävs för att nå ett önskat minimibelopp.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>In-guest VM-mått med Azure diagnostics-tillägget
Azure diagnostics-tillägget är en agent som körs i en VM-instans. Agenten övervakar och sparar prestandamått i Azure-lagring. Dessa prestandamått innehåller mer detaljerad information om status för den virtuella datorn, till exempel *AverageReadTime* för diskar eller *PercentIdleTime* för CPU. Du kan skapa regler för automatisk skalning baserat på en mer detaljerad medvetenhet om den virtuella datorns prestanda, inte bara procentandelen CPU-användning eller minnesförbrukning.

Om du vill använda Azure Diagnostics-tillägget måste du skapa Azure-lagringskonton för dina VM-instanser, installera Azure Diagnostics-agenten och sedan konfigurera de virtuella datorerna för att strömma specifika prestandaräknare till lagringskontot.

Mer information finns i artiklarna om att aktivera Azure Diagnostics-tillägget på en [virtuell Linux-dator](../virtual-machines/extensions/diagnostics-linux.md) eller en [virtuell Windows-dator](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Mätvärden på programnivå med appstatistik
Om du vill få mer insyn i dina program kan du använda Application Insights. Du installerar ett litet instrumenteringspaket i ditt program som övervakar appen och skickar telemetri till Azure. Du kan övervaka mått som svarstider för ditt program, sidans inläsningsprestanda och antalet sessioner. Dessa programmått kan användas för att skapa regler för automatisk skalning på en detaljerad och inbäddad nivå när du utlöser regler baserat på användbara insikter som kan påverka kundupplevelsen.

Mer information om App Insights finns i artikeln [Vad är Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Schemalagd automatisk skalning
Du kan också skapa regler för automatisk skalning baserat på scheman. Med de här schemabaserade reglerna kan du automatiskt skala antalet VM-instanser vid fasta tider. Med prestandabaserade regler kan det finnas en prestandapåverkan på programmet innan regler för automatisk skalning utlöses och de nya VM-instanserna etableras. Om du kan förutse en sådan efterfrågan etableras ytterligare VM-instanser och är klara för ytterligare kundanvändning och programbehov.

Följande exempel är scenarier som kan gynna användningen av schemabaserade regler för automatisk skalning:

- Skala automatiskt ut antalet VM-instanser i början av arbetsdagen när kundernas efterfrågan ökar. I slutet av arbetsdagen skalas automatiskt antalet VM-instanser för att minimera resurskostnaderna över natten när programanvändningen är låg.
- Om en avdelning använder ett program kraftigt vid vissa delar av månaden eller räkenskapscykeln skalar du automatiskt antalet VM-instanser för att tillgodose deras ytterligare krav.
- När det finns ett marknadsföringsevenemang, marknadsföring eller semesterförsäljning kan du automatiskt skala antalet VM-instanser före förväntad kundefterfrågan. 


## <a name="next-steps"></a>Nästa steg
Du kan skapa regler för automatisk skalning som använder värdbaserade mått med något av följande verktyg:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure-mall](tutorial-autoscale-template.md)

I den här översikten beskrivs hur du använder regler för automatisk skalning för att skala vågrätt och öka eller minska *antalet* VM-instanser i skalningsuppsättningen. Du kan också skala lodrätt för att öka eller minska *vm-instansens storlek*. Mer information finns i [Lodrät automatisk skalning med uppsättningar med virtuell datorskala](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar vm-instanser finns i [Hantera skalningsuppsättningar för virtuella datorer med Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Mer information om hur du genererar aviseringar när reglerna för automatisk skalning utlöses finns i [Använda åtgärder för automatisk skalning för att skicka e-post- och webhook-aviseringar i Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Du kan också [använda granskningsloggar för att skicka e-post- och webhook-aviseringar i Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
