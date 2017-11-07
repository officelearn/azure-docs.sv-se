---
title: "Övervaka behållare i Azure Service Fabric med OMS Log Analytics | Microsoft Docs"
description: "Använd OMS Log Analytics för övervakning av behållare som körs i Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 17121041520160d0d76832bbdbe74ad6a649fdd8
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="monitor-containers-with-oms-log-analytics"></a>Övervaka behållare med OMS logganalys
 
Den här artikeln beskriver de steg som krävs för att ställa in behållaren övervakning för klustret. Mer information finns i [övervakning behållare i Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Om du vill se en stegvis självstudiekurs om det här kan du också följa [övervaka Windows-behållare i Service Fabric med OMS](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Ställ in behållaren övervakningslösning

> [!NOTE]
> Du måste till OMS Log Analytics har ställts in för ditt kluster som har distribuerats på noderna OMS-agenten. Om du inte följer du stegen i [ställa in OMS logganalys](service-fabric-diagnostics-oms-setup.md) och [lägga till OMS-Agent till ett kluster](service-fabric-diagnostics-oms-agent.md) första.

1. När klustret har konfigurerats med OMS logganalys och OMS-agenten kan du distribuera din behållare. Vänta tills behållarna som ska distribueras innan du fortsätter till nästa steg.

2. I Azure Marketplace, söker du efter *lösning för övervakning av behållare* och klicka på den **lösning för övervakning av behållare** resurs som visas under övervakning + Management kategori.

    ![Lägga till behållare lösning](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Skapa lösningen i samma arbetsyta som redan har skapats för klustret. Den här ändringen utlöser automatiskt agenten för att börja samla in data för docker på behållaren. Du bör se lösningen lysa med inkommande loggar och statistik i cirka 15 minuter eller liknande.

## <a name="next-steps"></a>Nästa steg
* Läs mer om behållaren orchestration på Service Fabric - [Service Fabric och behållare](service-fabric-containers-overview.md)
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
* Konfigurera OMS att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) regler för att underlätta identifiering och diagnostik