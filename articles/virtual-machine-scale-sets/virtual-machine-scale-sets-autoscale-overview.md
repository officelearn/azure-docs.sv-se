---
title: Översikt över Autoskala med skalningsuppsättningar i virtuella Azure-datorn | Microsoft Docs
description: Lär dig mer om de olika sätten att du kan skala automatiskt en virtuell dator i Azure-skala in baserat på prestanda eller ett fast schema
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 03053f8427fbd20b0a7288d930dca258ee3070b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Översikt över Autoskala med en virtuell dator i Azure skala anger
En skaluppsättning för virtuell dator i Azure kan automatiskt öka eller minska antalet VM-instanser som kör programmet. Det här beteendet för automatisk och elastiska minskar de hanteringskostnader för att övervaka och optimera prestanda för ditt program. Du kan skapa regler som definierar minimalt acceptabel prestanda för en positiv kundupplevelse. När dessa definierade tröskelvärden uppfylls vidta Autoskala regler åtgärder för att justera kapaciteten för din skaluppsättning. Du kan också schemalägga händelser för att automatiskt öka eller minska kapacitet för din skaluppsättning på fast gånger. Den här artikeln innehåller en översikt av vilka mått som är tillgängliga och vilka åtgärder Autoskala kan utföra.


## <a name="benefits-of-autoscale"></a>Fördelarna med Autoskala
Om ditt program begäran ökar, ange ökar belastningen på VM-instanser i nivå. Om den här ökade belastningen är konsekvent, i stället för bara en kort begäran, kan du konfigurera automatiska regler för att öka antalet VM-instanser i skaluppsättning.

När dessa VM-instanser som skapas och dina program distribueras börjar skaluppsättning distribuera trafik till dem via belastningsutjämnaren. Du kan styra vilka mått som ska övervakas, t.ex CPU eller minne, hur länge belastningen program måste uppfylla ett visst tröskelvärde och ange hur många VM-instanser som ska läggas till skalan.

På en kväll eller helger minska program-begäran. Om det här minskar belastningen är konsekvent under en viss tidsperiod, kan du konfigurera automatiska regler för att minska antalet VM-instanser i skaluppsättning. Den här åtgärden för skala minskar kostnaden för att köra skaluppsättningen eftersom du bara köra antalet instanser som krävs för att uppfylla den aktuella begäran.


## <a name="use-host-based-metrics"></a>Använda värdbaserad mått
Du kan skapa automatiska regler som tillgängliga mått för inbyggda värden från VM-instanser. Värden mått ger inblick i prestanda för VM-instanser i en skala utan att behöva installera eller konfigurera ytterligare agenter och datasamlingar. Autoskala regler som använder de här måtten kan skala ut eller i antal VM-instanser som svar på CPU-användning, minne begäran eller åtkomst till disken.

Regler för automatisk skalning som använder värdbaserade mått kan skapas med något av följande verktyg:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI 2.0](tutorial-autoscale-cli.md)
- [Azure-mall](tutorial-autoscale-template.md)

Om du vill skapa automatiska regler som använder mer detaljerad prestandamått, kan du [installera och konfigurera Azure diagnostics tillägget](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) på VM-instanser eller [konfigurera din App Insights använder](#application-level-metrics-with-app-insights).

Autoskala regler som använder värdbaserad mått,-Gäst VM mått med Azure-diagnostik tillägget och App Insights kan använda följande konfigurationsinställningar.

### <a name="metric-sources"></a>Mått källor
Autoskala regler kan använda mått från någon av följande källor:

| Måttkälla        | Användningsfall                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuella skaluppsättning    | För värdbaserade mått som inte kräver ytterligare agenter som ska installeras eller konfigureras.                                  |
| Lagringskonto      | Azure-diagnostik tillägget skriver prestandamått till Azure-lagring som används för att utlösa Autoskala regler. |
| Service Bus-kö    | Programmet eller andra komponenter kan skicka meddelanden i en Azure Service Bus-kö i regler för utlösare.                   |
| Application Insights | Ett instrumentation paket installeras i ditt program som strömmas mått direkt från appen.                         |


### <a name="autoscale-rule-criteria"></a>Autoskala villkor
Följande värdbaserad mått är tillgängliga för användning när du skapar automatiska regler. Om du använder Azure-diagnostik tillägget eller App Insights kan definiera du vilka mått för att övervaka och använda med Autoskala regler.

| Måttnamn               |
|---------------------------|
| Processorprocentandel            |
| Nätverk in                |
| Nätverk ut               |
| Lästa byte på disk           |
| Skrivna byte på disk          |
| Läsåtgärder på disk/sek  |
| Skrivåtgärder på disk/sek |
| Återstående CPU-krediter     |
| Förbrukade CPU-krediter      |

När du skapar automatiska regler för att övervaka ett visst mått reglerna att titta på en sammanställning av följande mått:

| Sammansättningstyp |
|------------------|
| Medel          |
| Minimum          |
| Maximal          |
| Totalt            |
| Sista             |
| Antal            |

Regler för automatiska sedan utlöses när mätvärdena jämförs med din angivet tröskelvärde med något av följande operatorer:

| Operator                 |
|--------------------------|
| Större än             |
| Större än eller lika med |
| Mindre än                |
| Mindre än eller lika med    |
| Lika med                 |
| Inte lika med             |


### <a name="actions-when-rules-trigger"></a>Åtgärder när regler utlösa
När en regel utlösare Autoskala skala din skaluppsättning automatiskt i något av följande sätt:

| Skalningsåtgärden     | Användningsfall                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Öka antal med   | Ett fast antal VM-instanser för att skapa. Användbart i skalningsuppsättningar med ett mindre antal virtuella datorer.                                           |
| Öka procent med | En del-baserade ökning av VM-instanser. Bra för större skala anger där en fast ökning kan avsevärt förbättrar inte prestandan. |
| Öka antal till   | Skapa många VM-instanser som krävs för att nå en önskad längsta.                                                            |
| Minska antal till   | Ett fast antal VM-instanser som ska tas bort. Användbart i skalningsuppsättningar med ett mindre antal virtuella datorer.                                           |
| Minska procent med | En del-baserade minskning av VM-instanser. Bra för större skala anger där en fast ökning kan avsevärt minskar inte resursförbrukning och kostnader. |
| Minska antal till   | Ta bort många VM-instanser som krävs för att nå önskad minsta möjliga.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>-Gäst VM mått med filnamnstillägget Azure-diagnostik
Tillägget Azure diagnostics är en agent som körs i en VM-instans. Agenten övervakar och sparar prestandamått till Azure-lagring. Dessa prestandamått innehåller mer detaljerad information om statusen för den virtuella datorn, till exempel *AverageReadTime* för diskar eller *PercentIdleTime* för CPU. Du kan skapa automatiska regler baserat på en mer detaljerad medvetenhet om VM-prestanda, inte bara procent av processoranvändningen för användning eller minne.

Om du vill använda Azure diagnostics-tillägget, måste du skapa Azure storage-konton för VM-instanser, installera agenten Azure-diagnostik och sedan konfigurera de virtuella datorerna till dataströmmen specifika prestandaräknare till lagringskontot.

Mer information finns i artiklarna om att aktivera Azure-diagnostiktillägget på en [virtuell Linux-dator](../virtual-machines/linux/diagnostic-extension.md) eller en [virtuell Windows-dator](../virtual-machines/windows/ps-extensions-diagnostics.md).


## <a name="application-level-metrics-with-app-insights"></a>Programnivå mått med App Insights
Du kan använda Application Insights för att få mer synlighet i prestanda för dina program. Du installerar ett litet instrumentation paket i programmet som övervakar appen och skickar telemetri till Azure. Du kan övervaka svarstider för programmet, sidan belastningen prestanda, och räknar sessionen. De här måtten för programmet kan användas för att skapa automatiska regler på en detaljerad och inbäddade nivå som du utlösa regler baserat på tillämplig insikter som kan påverka kundupplevelsen.

Mer information om App Insights finns i artikeln [Vad är Application Insights](../application-insights/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Schemalagda Autoskala
Du kan också skapa automatiska regler baserat på scheman. Reglerna schemabaserade kan du automatiskt skala antalet VM-instanser på fast gånger. Det kan finnas en prestandapåverkan på ansökan innan Autoskala regler utlösaren med regler med prestanda och nya VM-instanser har etablerats. Om du kan kommer sådana begäran är ytterligare VM-instanser allokerade och redo för ytterligare kundens användning och tillämpning begäran.

I följande exempel finns scenarier som kan få användning av schemabaserade Autoskala regler:

- Skala automatiskt antalet VM-instanser i början av arbetsdagen när kundefterfrågan ökar. I slutet av arbetsdagen skala automatiskt i antal VM-instanser för att minimera resurskostnader över natten när användningen är låg.
- Om en avdelning använder ett program som är kraftigt på vissa delar av månaden eller fiskala cykel, skala automatiskt antalet VM-instanser för de ytterligare krav som ställs.
- När det finns en marknadsföring händelse, befordran eller helgdag försäljning, kan du automatiskt skala antalet VM-instanser i förväntade kundernas behov. 


## <a name="next-steps"></a>Nästa steg
Du kan skapa automatiska regler som använder värdbaserad mått med något av följande verktyg:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI 2.0](tutorial-autoscale-cli.md)
- [Azure-mall](tutorial-autoscale-template.md)

Den här översikten beskrivs hur du använder automatiska regler för att skala horisontellt och öka eller minska den *nummer* i VM-instanser i nivå. Du kan även skala lodrätt för att öka eller minska den Virtuella datorinstansen *storlek*. Mer information finns i [lodräta Autoskala med Virtual Machine-skalningsuppsättningar](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar dina VM-instanser finns [hantera virtuella skalningsuppsättningarna med Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Information om hur du genererar aviseringar när din Autoskala regler utlösaren finns [använda automatiska åtgärder för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Du kan också [Använd granskningsloggarna för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).