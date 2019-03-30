---
title: Övervaka behållare i Azure Service Fabric med Azure Monitor-loggar | Microsoft Docs
description: Använda Azure Monitor-loggar för övervakning av behållare som körs på Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d03d68560502821b9c343be983d9f7b5a83ed977
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663208"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Övervaka behållare med Azure Monitor-loggar
 
Den här artikeln beskriver de steg som krävs för att konfigurera Azure Monitor-loggar behållarens övervakningslösning att visa behållarhändelser. Om du vill konfigurera ditt kluster för att samla in behållarhändelser, se den här [stegvis självstudie](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Konfigurera behållarens övervakningslösning

> [!NOTE]
> Du måste ha Azure Monitor loggar för ditt kluster som har distribuerats på noderna Log Analytics-agenten. Om du inte följer du stegen i [konfigurera Azure Monitor-loggar](service-fabric-diagnostics-oms-setup.md) och [lägga till Log Analytics-agenten i ett kluster](service-fabric-diagnostics-oms-agent.md) första.

1. När klustret är konfigurerat med Azure Monitor-loggar och Log Analytics-agenten kan du distribuera dina behållare. Vänta tills din behållare som ska distribueras innan du fortsätter till nästa steg.

2. I Azure Marketplace, söker du efter *lösning för övervakning av behållare* och klicka på den **lösning för övervakning av behållare** resurs som visas under övervakning + hantering kategori.

    ![Lägga till containerlösning](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Skapa lösningen i samma arbetsyta som redan har skapats för klustret. Den här ändringen utlöser automatiskt agenten för att börja samla in data för docker på behållarna. I cirka 15 minuter eller så bör du se lösningen tänds med inkommande loggar och statistik, som visas i bilden nedan.

    ![Grundläggande Log Analytics-instrumentpanelen](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenten aktiverar insamlingen av flera behållare loggar som kan efterfrågas i Azure Monitor-loggar eller används för att visualisera nyckeltal. Loggtyper som samlas in är:

* ContainerInventory: Visar information om behållarens plats, namn och bilder
* ContainerImageInventory: information om distribuerade avbildningar, inklusive ID: N eller storlekar
* ContainerLog: specifika felloggar dockerloggar (stdout o.s.v.) och andra poster
* ContainerServiceLog: docker-daemon-kommandon som har körts
* Prestanda: prestandaräknare, inklusive behållare processor, minne, nätverkstrafik, disk-i/o och anpassade mått från värddatorerna



## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Monitor loggar behållarlösningen](../azure-monitor/insights/containers.md).
* Läs mer om orkestrering av behållare i Service Fabric - [Service Fabric och behållare](service-fabric-containers-overview.md)
* Bekanta dig med den [loggsökning och frågor](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av Azure Monitor-loggar
* Konfigurera Azure Monitor-loggar att ställa in [automatiserade aviseringar](../log-analytics/log-analytics-alerts.md) regler för att underlätta identifiering och diagnostik