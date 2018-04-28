---
title: Övervaka behållare i Azure Service Fabric med Log Analytics | Microsoft Docs
description: Använd Log Analytics för övervakning av behållare som körs i Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 1de7e58eecc80e306920ab17884290dfddf8efa8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Övervakaren behållare med logganalys
 
Den här artikeln beskriver de steg som krävs för installationen av behållaren OMS logganalys övervakningslösning visa händelser för behållaren. Om du vill konfigurera klustret för att samla in händelser för behållaren finns [stegvisa självstudier](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Ställ in behållaren övervakningslösning

> [!NOTE]
> Du måste ha logganalys för ett kluster som har distribuerats på noderna OMS-agenten. Om du inte följer du stegen i [konfigurera logganalys](service-fabric-diagnostics-oms-setup.md) och [lägga till OMS-Agent till ett kluster](service-fabric-diagnostics-oms-agent.md) första.

1. När klustret har konfigurerats med logganalys och OMS-agenten kan du distribuera din behållare. Vänta tills behållarna som ska distribueras innan du fortsätter till nästa steg.

2. I Azure Marketplace, söker du efter *lösning för övervakning av behållare* och klicka på den **lösning för övervakning av behållare** resurs som visas under övervakning + Management kategori.

    ![Lägga till behållarlösning](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Skapa lösningen i samma arbetsyta som redan har skapats för klustret. Den här ändringen utlöser automatiskt agenten för att börja samla in data för docker på behållaren. Du bör se lösningen lysa med inkommande loggar och statistik i cirka 15 minuter eller liknande. som visas i bilden nedan.

    ![Grundläggande OMS-instrumentpanelen](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenten aktiverar insamlingen av flera behållare-specifika loggar som kan frågas i OMS eller användas för visualiserade nyckeltal. Log-typer som samlas in är:

* ContainerInventory: Visar information om behållarens plats, namn och bilder
* ContainerImageInventory: information om distribuerade bilder, inklusive ID eller storlekar
* ContainerLog: specifika felloggar, docker-loggar (stdout osv.) och andra transaktioner
* ContainerServiceLog: docker daemon kommandon som har körts
* Prestanda: prestandaräknare inklusive behållaren cpu, minne, nätverkstrafik, disk-i/o och anpassade mått från värddatorerna



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [OMSS behållare lösning](../log-analytics/log-analytics-containers.md).
* Läs mer om behållaren orchestration på Service Fabric - [Service Fabric och behållare](service-fabric-containers-overview.md)
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
* Konfigurera logganalys att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) regler för att underlätta identifiering och diagnostik