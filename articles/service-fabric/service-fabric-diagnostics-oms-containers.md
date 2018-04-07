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
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Övervakaren behållare med logganalys
 
Den här artikeln beskriver de steg som krävs för att ställa in behållaren övervakning för klustret. Mer information finns i [övervakning behållare i Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Om du vill se en stegvis självstudiekurs om det här kan du också följa [övervaka Windows-behållare i Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Ställ in behållaren övervakningslösning

> [!NOTE]
> Du måste ha logganalys för ett kluster som har distribuerats på noderna OMS-agenten. Om du inte följer du stegen i [konfigurera logganalys](service-fabric-diagnostics-oms-setup.md) och [lägga till OMS-Agent till ett kluster](service-fabric-diagnostics-oms-agent.md) första.

1. När klustret har konfigurerats med logganalys och OMS-agenten kan du distribuera din behållare. Vänta tills behållarna som ska distribueras innan du fortsätter till nästa steg.

2. I Azure Marketplace, söker du efter *lösning för övervakning av behållare* och klicka på den **lösning för övervakning av behållare** resurs som visas under övervakning + Management kategori.

    ![Lägga till behållarlösning](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Skapa lösningen i samma arbetsyta som redan har skapats för klustret. Den här ändringen utlöser automatiskt agenten för att börja samla in data för docker på behållaren. Du bör se lösningen lysa med inkommande loggar och statistik i cirka 15 minuter eller liknande.

## <a name="next-steps"></a>Nästa steg
* Läs mer om behållaren orchestration på Service Fabric - [Service Fabric och behållare](service-fabric-containers-overview.md)
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
* Konfigurera logganalys att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) regler för att underlätta identifiering och diagnostik