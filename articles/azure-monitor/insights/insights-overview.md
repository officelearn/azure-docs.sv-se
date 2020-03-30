---
title: Översikt över insikter i Azure Monitor | Microsoft-dokument
description: Insikter ger en anpassad övervakningsupplevelse i Azure Monitor för särskilda program och tjänster. Den här artikeln innehåller en kort beskrivning av var och en av de insikter som för närvarande är tillgängliga.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657256"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Översikt över insikter i Azure Monitor
Insikter ger en anpassad övervakningsupplevelse för särskilda program och tjänster. De lagrar data i [Azure Monitor-dataplattformen](../platform/data-platform.md) och utnyttjar andra Azure Monitor-funktioner för analys och avisering, men kan samla in ytterligare data och ge en unik användarupplevelse i Azure-portalen. Få tillgång till insikter från avsnittet **Insikter** på Azure Monitor-menyn i Azure-portalen.

Följande avsnitt ger en kort beskrivning av de insikter som för närvarande är tillgängliga i Azure Monitor. Mer information om var och en finns i den detaljerade dokumentationen.

## <a name="application-insights"></a>Application Insights
Application Insights är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Det fungerar för program på en mängd olika plattformar, inklusive .NET, Node.js och Java EE, värd lokalt, hybrid, eller något offentligt moln. Den integreras också med din DevOps-process och har anslutningspunkter till en mängd olika utvecklingsverktyg.

Se [Vad är Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
Azure Monitor for containers övervakar prestanda för behållararbetsbelastningar som distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Övervakning av dina behållare är viktigt, särskilt när du kör ett produktionskluster, i stor skala, med flera program.

Se [översikt över Azure Monitor för behållare](../insights/container-insights-overview.md).

![Azure Monitor för containrar](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor för resursgrupper (förhandsgranskning)
Azure Monitor för resursgrupper hjälper till att triage och diagnostisera eventuella problem som dina enskilda resurser stöter på, samtidigt som de erbjuder kontext om resursgruppens hälsa och prestanda som helhet.

Se [Övervaka resursgrupper med Azure Monitor (förhandsversion)](../insights/resource-group-insights.md).

![Azure Monitor för resursgrupper](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor för virtuella datorer (förhandsgranskning)
Azure Monitor för virtuella datorer övervakar dina Azure-virtuella datorer (VM) och skaluppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer.

Se [Vad är Azure Monitor för virtuella datorer?](vminsights-overview.md)

![Azure Monitor för virtuella datorer](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor för nätverk (förhandsversion)
[Azure Monitor för nätverk](network-insights-overview.md) ger en omfattande översikt över hälso- och mått för alla nätverksresurser. Den avancerade sökfunktionen hjälper dig att identifiera resursberoenden, vilket möjliggör scenarier som att identifiera resurser som är värd för din webbplats genom att helt enkelt söka efter ditt webbplatsnamn.

![Azure Monitor för nätverk](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Monitor-dataplattformen](../platform/data-platform.md) som utnyttjas av insikter.
* Lär dig mer om de olika [datakällor som används av Azure Monitor](../platform/data-sources.md) och de olika typer av data som samlas in av var och en av insikterna.
