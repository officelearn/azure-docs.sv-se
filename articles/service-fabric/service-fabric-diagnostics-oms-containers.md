---
title: Övervaka behållare i Azure Service Fabric med Log Analytics | Microsoft Docs
description: Använda Log Analytics för att övervaka behållare som körs på Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: aabdae370c28f8fa633372be4505c00c25254408
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403258"
---
# <a name="monitor-containers-with-log-analytics"></a>Övervaka behållare med Log Analytics
 
Den här artikeln beskriver de steg som krävs för att ställa in Azure Log Analytics-behållarens övervakningslösning att visa behållarhändelser. Om du vill konfigurera ditt kluster för att samla in behållarhändelser, se den här [stegvis självstudie](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Konfigurera behållarens övervakningslösning

> [!NOTE]
> Du måste ha Log Analytics set upp för klustret som har distribuerats på noderna Log Analytics-agenten. Om du inte följer du stegen i [konfigurera Log Analytics](service-fabric-diagnostics-oms-setup.md) och [lägga till Log Analytics-agenten i ett kluster](service-fabric-diagnostics-oms-agent.md) första.

1. När klustret har konfigurerats med Log Analytics och Log Analytics-agenten kan du distribuera dina behållare. Vänta tills din behållare som ska distribueras innan du fortsätter till nästa steg.

2. I Azure Marketplace, söker du efter *lösning för övervakning av behållare* och klicka på den **lösning för övervakning av behållare** resurs som visas under övervakning + hantering kategori.

    ![Lägga till containerlösning](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Skapa lösningen i samma arbetsyta som redan har skapats för klustret. Den här ändringen utlöser automatiskt agenten för att börja samla in data för docker på behållarna. I cirka 15 minuter eller så bör du se lösningen tänds med inkommande loggar och statistik, som visas i bilden nedan.

    ![Grundläggande Log Analytics-instrumentpanelen](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenten aktiverar insamlingen av flera behållare loggar som kan efterfrågas i Log Analytics eller används för att visualisera nyckeltal. Loggtyper som samlas in är:

* ContainerInventory: Visar information om behållarens plats, namn och bilder
* ContainerImageInventory: information om distribuerade avbildningar, inklusive ID: N eller storlekar
* ContainerLog: specifika felloggar dockerloggar (stdout o.s.v.) och andra poster
* ContainerServiceLog: docker-daemon-kommandon som har körts
* Prestanda: prestandaräknare, inklusive behållare processor, minne, nätverkstrafik, disk-i/o och anpassade mått från värddatorerna



## <a name="next-steps"></a>Nästa steg
* Läs mer om [behållarlösningen för Log Analytics](../log-analytics/log-analytics-containers.md).
* Läs mer om orkestrering av behållare i Service Fabric - [Service Fabric och behållare](service-fabric-containers-overview.md)
* Bekanta dig med den [loggsökning och frågor](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av Log Analytics
* Konfigurera Log Analytics för att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) regler för att underlätta identifiering och diagnostik