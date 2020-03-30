---
title: Övervaka behållare med Azure Monitor-loggar
description: Använd Azure Monitor-loggar för övervakning av behållare som körs på Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614442"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Övervaka behållare med Azure Monitor-loggar
 
Den här artikeln beskriver de steg som krävs för att konfigurera Azure Monitor-loggbehållarens övervakningslösning för att visa behållarhändelser. Om du vill konfigurera klustret för att samla in behållarhändelser läser du den här [steg-för-steg-självstudien](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Konfigurera behållarövervakningslösningen

> [!NOTE]
> Du måste ha Azure Monitor-loggar konfigurerade för klustret samt ha Log Analytics-agenten distribuerad på dina noder. Om du inte gör det följer du stegen i [Konfigurera Azure Monitor-loggar](service-fabric-diagnostics-oms-setup.md) och [Lägg till Log Analytics-agenten i ett kluster](service-fabric-diagnostics-oms-agent.md) först.

1. När klustret har konfigurerats med Azure Monitor-loggar och Log Analytics-agenten kan du distribuera dina behållare. Vänta tills behållarna har distribuerats innan du går vidare till nästa steg.

2. I Azure Marketplace söker du efter lösning för *behållarövervakning* och klickar på resursen **lösning för behållarövervakning** som visas under kategorin Övervakning + hantering.

    ![Lägga till containerlösning](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Skapa lösningen på samma arbetsyta som redan har skapats för klustret. Den här ändringen utlöser automatiskt agenten för att börja samla in dockerdata på behållarna. I ca 15 minuter eller så, bör du se lösningen lysa upp med inkommande loggar och statistik, som visas i bilden nedan.

    ![Instrumentpanel för grundläggande logganalys](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenten aktiverar insamling av flera behållarspecifika loggar som kan efterfrågas i Azure Monitor-loggar eller användas för att visualisera prestandaindikatorer. De loggtyper som samlas in är:

* BehållareInventory: visar information om behållarplats, namn och bilder
* ContainerImageInventory: information om distribuerade avbildningar, inklusive ID eller storlekar
* ContainerLog: specifika felloggar, dockerloggar (stdout, etc.) och andra poster
* ContainerServiceLog: docker daemon kommandon som har körts
* Perf: prestandaräknare inklusive behållarprocessor, minne, nätverkstrafik, disk-i/o och anpassade mått från värddatorerna



## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Monitor-loggbehållare lösning](../azure-monitor/insights/containers.md).
* Läs mer om containerorkestrering på Service Fabric - [Service Fabric och behållare](service-fabric-containers-overview.md)
* Bekanta dig med [loggsöknings- och frågefunktionerna](../log-analytics/log-analytics-log-searches.md) som erbjuds som en del av Azure Monitor-loggar
* Konfigurera Azure Monitor-loggar för att ställa in [automatiska varningsregler](../log-analytics/log-analytics-alerts.md) för att hjälpa till att identifiera och diagnostik