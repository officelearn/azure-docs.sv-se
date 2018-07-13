---
title: Översikt över automatisk skalning med Azure VM scale sets | Microsoft Docs
description: Lär dig mer om de olika sätten att du automatiskt kan skala en Azure VM-skalningsuppsättning in baserat på prestanda eller enligt ett fast schema
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48e64f0cc65ade870425f73989209e8bef8ec8d5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630294"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Översikt över automatisk skalning med Azure-datorer anger
En Azure VM-skalningsuppsättning kan automatiskt öka eller minska antalet Virtuella datorinstanser som kör programmet. Detta automatiserad och elastiska minskar hanteringsomkostnaderna för att övervaka och optimera prestanda för ditt program. Du kan skapa regler som definierar acceptabel prestanda för en positiv kundupplevelse. När de definierade tröskelvärdena är uppfyllda, regler för automatisk skalning att vidta åtgärder för att justera kapaciteten för din skalningsuppsättning. Du kan även schemalägga händelser att automatiskt öka eller minska kapaciteten för din skalningsuppsättning på fast gånger. Den här artikeln innehåller en översikt över vilka mått som är tillgängliga och vilka åtgärder som automatisk skalning kan utföra.


## <a name="benefits-of-autoscale"></a>Fördelarna med automatisk skalning
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen.

När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via belastningsutjämnaren. Du kan styra vilka mått som ska övervakas, som CPU eller minne, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och ange hur många Virtuella datorinstanser att lägga till i skalan.

På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.


## <a name="use-host-based-metrics"></a>Använd värdbaserade mått
Du kan skapa regler för automatisk skalning som inbyggda värdmått som är tillgängliga från dina VM-instanser. Värdmått ger dig insyn i prestanda för de Virtuella datorinstanserna i en skalningsuppsättning utan att behöva installera eller konfigurera ytterligare agenter och datasamlingar. Regler för automatisk skalning som använder de här måtten kan skala in eller ut antalet Virtuella datorinstanser som svar på CPU-användning, minneskrav eller åtkomst till disken.

Regler för automatisk skalning som använder värdbaserade mått kan skapas med något av följande verktyg:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI 2.0](tutorial-autoscale-cli.md)
- [Azure-mall](tutorial-autoscale-template.md)

För att skapa regler för automatisk skalning som använder mer detaljerade prestandavärden, kan du [installera och konfigurera Azure diagnostics-tillägg](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) på VM-instanser eller [konfigurera din App Insights användningen](#application-level-metrics-with-app-insights).

Regler för automatisk skalning som använder värdbaserade mått, i gäst VM-mått med Azure-diagnostiktillägget och App Insights kan använda följande konfigurationsinställningar.

### <a name="metric-sources"></a>Metrisk källor
Regler för automatisk skalning kan använda mått från någon av följande källor:

| Måttkälla        | Användningsfall                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuella skalningsuppsättning    | För värdbaserade mått som inte kräver ytterligare agenter som ska installeras eller konfigureras.                                  |
| Lagringskonto      | Azure-diagnostiktillägget skriver prestandamått till Azure-lagring som används sedan för att utlösa regler för automatisk skalning. |
| Service Bus-kö    | Programmet eller andra komponenter kan överföra meddelanden i en Azure Service Bus-kö utlösaren reglerna.                   |
| Application Insights | En instrumentationspaket installerad i ditt program som strömmas mått direkt från appen.                         |


### <a name="autoscale-rule-criteria"></a>Villkor för automatisk skalning
Följande värdbaserade måtten är tillgängliga för användning när du skapar regler för automatisk skalning. Om du använder Azure-diagnostiktillägget eller App Insights kan definiera du vilka mått för att övervaka och använda med regler för automatisk skalning.

| Måttnamn               |
|---------------------------|
| Procent CPU            |
| Nätverk in                |
| Nätverk ut               |
| Lästa byte på disk           |
| Skrivna byte på disk          |
| Läsåtgärder på disk/sek  |
| Skrivåtgärder på disk/sek |
| Återstående CPU-krediter     |
| Förbrukade CPU-krediter      |

När du skapar regler för automatisk skalning att övervaka ett visst mått, titta reglerna på något av följande åtgärder för sammanställning av mått:

| Sammansättningstyp |
|------------------|
| Medel          |
| Minimum          |
| Maximal          |
| Totalt            |
| Sista             |
| Antal            |

Regler för automatisk skalning sedan utlöses när mått som jämförs med tröskeln för ditt definierade med någon av följande operatorer:

| Operator                 |
|--------------------------|
| Större än             |
| Större än eller lika med |
| Mindre än                |
| Mindre än eller lika med    |
| Lika med                 |
| Inte lika med             |


### <a name="actions-when-rules-trigger"></a>Åtgärder när regler utlöser
När regeln utlöses en automatisk skalning kan din skalningsuppsättning automatiskt skala i något av följande sätt:

| Skalningsåtgärden     | Användningsfall                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Öka antal med   | Ett fast antal VM-instanser för att skapa. Användbart i skalningsuppsättningar med ett mindre antal virtuella datorer.                                           |
| Öka procent med | En procentandel-baserade ökning av VM-instanser. Bra för större skala anger där en fast ökning kan inte märkbart förbättra prestandan. |
| Öka antal till   | Skapa så många Virtuella datorinstanser som krävs för att nå en önskad längsta.                                                            |
| Minska antal till   | Ett fast antal VM-instanser för att ta bort. Användbart i skalningsuppsättningar med ett mindre antal virtuella datorer.                                           |
| Minska procent med | En procentandel-baserade minskning av VM-instanser. Bra för större skala anger där en fast ökning kan avsevärt minska inte resursförbrukning och kostnader. |
| Minska antal till   | Ta bort eftersom många Virtuella datorinstanser som krävs för att nå den önskade minimimängd.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>I gäst VM-mått med Azure diagnostics-tillägg
Azure diagnostics-tillägg är en agent som körs på en VM-instans. Agenten övervakar och sparar prestandamått i Azure storage. De här prestandavärdena innehåller mer detaljerad information om status för den virtuella datorn, till exempel *AverageReadTime* för diskar eller *PercentIdleTime* för CPU. Du kan skapa regler för automatisk skalning baserat på en mer detaljerad medvetenhet om VM-prestanda, inte bara procent av processoranvändningen för användning eller minne.

För att använda Azure diagnostics-tillägg, måste du skapa Azure storage-konton för dina VM-instanser, installera Azure diagnostics-agenten och konfigurera de virtuella datorerna till stream specifika prestandaräknare till lagringskontot.

Mer information finns i artiklarna om att aktivera Azure-diagnostiktillägget på en [virtuell Linux-dator](../virtual-machines/extensions/diagnostics-linux.md) eller en [virtuell Windows-dator](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Programnivå mått med App Insights
Du kan använda Application Insights för att få bättre översyn i, till programmens prestanda. Du installerar ett litet instrumentationspaket i ditt program som övervakar appen och skickar telemetri till Azure. Du kan övervaka mått, till exempel svarstiderna för ditt program, sidinläsningsprestanda, och sessionen räknas. Dessa mätvärden för application kan användas för att skapa regler för automatisk skalning på en detaljerad och inbäddade nivå som du utlösa regler baserat på värdefulla insikter som kan påverka kundupplevelsen.

Mer information om App Insights finns i artikeln [Vad är Application Insights](../application-insights/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Schemalagd automatisk skalning
Du kan också skapa regler för automatisk skalning baserat på scheman. Reglerna schemabaserade gör att du kan automatiskt skala antalet instanser av virtuella datorer på fast gånger. Det kan förekomma en prestandapåverkan på programmet innan du automatisk skalning regler utlösaren med prestandabaserad regler och de nya Virtuella datorinstanserna har etablerats. Om du kan förutse sådana begäran är ytterligare VM-instanser etablerade och redo för kundernas användning och programmet behov.

I följande exempel finns scenarier som kan ha nytta av användningen av regler för schemabaserad automatisk skalning:

- Automatiskt skala ut antalet Virtuella datorinstanser i början av arbetsdagen när kundernas efterfrågan ökar. I slutet av arbetsdagen skala automatiskt i antalet Virtuella datorinstanser att minimera resurskostnader över natten när användningen är låg.
- Om ett program använder kraftigt i en avdelning på vissa delar av månaden eller fiskala cykel, automatiskt skala antalet Virtuella datorinstanser att tillgodose de ytterligare krav som ställs.
- Om det finns en marknadsföring händelse, en befordran eller en helgdag försäljning, kan du automatiskt skala antalet Virtuella datorinstanser före förväntade kundernas efterfrågan. 


## <a name="next-steps"></a>Nästa steg
Du kan skapa regler för automatisk skalning som använder värdbaserade mått med något av följande verktyg:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI 2.0](tutorial-autoscale-cli.md)
- [Azure-mall](tutorial-autoscale-template.md)

Den här översikten beskrivs hur du använder regler för automatisk skalning att skala vågrätt och öka eller minska den *nummer* för VM-instanser i din skalningsuppsättning. Du kan även skala lodrätt om du vill öka eller minska den Virtuella datorinstansen *storlek*. Mer information finns i [vertikal automatisk skalning med VM Scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar dina VM-instanser finns i [hantera VM-skalningsuppsättningar med Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Läs hur du genererar aviseringar när dina autoskalningsregler utlösare i [använda åtgärder för automatisk skalning för att skicka e-post och webhook-aviseringar i Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Du kan också [Använd granskningsloggar för att skicka e-post och webhook-aviseringar i Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).