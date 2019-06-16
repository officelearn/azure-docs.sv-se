---
title: Översikt över Insights i Azure Monitor | Microsoft Docs
description: Insights tillhandahåller en anpassad övervakning upplevelse i Azure Monitor för specifika program och tjänster. Den här artikeln innehåller en kort beskrivning av var och en av de insikter som är tillgänglig för tillfället.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247236"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Översikt över Insights i Azure Monitor
Insights ger en anpassad upplevelse för övervakning för specifika program och tjänster. De lagra data i den [Azure Monitor dataplattform](../platform/data-platform.md) och använda andra funktioner med Azure Monitor för analys och varning, men kan samla in ytterligare data och ge en unik användarupplevelse i Azure-portalen. Få fram användbar information från den **Insights** avsnitt av Azure Monitor-menyn i Azure-portalen.

Följande avsnitt innehåller en kort beskrivning av de insikter som är tillgängliga i Azure Monitor. Se den detaljerade dokumentationen för mer information på var och en.

## <a name="application-insights"></a>Application Insights
Application Insights är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Det fungerar för program på en mängd olika plattformar, inklusive .NET, Node.js och Java EE finns på plats, hybrida eller offentliga moln. Den kan integreras med din DevOps-process och har kopplingspunkter till en rad olika utvecklingsverktyg.

Se [vad är Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor för behållare
Azure Monitor för behållare Övervakare prestanda för behållararbetsbelastningar distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Övervaka dina behållare är viktigt, särskilt när du kör ett produktionskluster i skala med flera program.

Se [Azure Monitor för översikt över behållare](../insights/container-insights-overview.md).

![Azure Monitor för behållare](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor för resursgrupper (förhandsversion)
Azure Monitor för resursgrupper som hjälper dig att hantera och diagnostisera problem uppstå din enskilda resurser och erbjuder sammanhang vad gäller hälsa och prestanda på resursgruppen som helhet.

Se [övervaka resursgrupper med Azure Monitor (förhandsversion)](../insights/resource-group-insights.md).

![Azure Monitor för resursgrupper](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor för virtuella datorer (förhandsversion)
Azure Monitor för virtuella datorer övervakar dina Azure-datorer (VM) och VM-skalningsuppsättningar i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer.

Se [vad är Azure Monitor för virtuella datorer?](vminsights-overview.md)

![Azure Monitor för virtuella datorer](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om den [Azure Monitor dataplattform](../platform/data-platform.md) utnyttjas av insikter.
* Lär dig mer om de olika [datakällor som används av Azure Monitor](../platform/data-sources.md) och olika typer av data som samlas in av var och en av insikterna.
