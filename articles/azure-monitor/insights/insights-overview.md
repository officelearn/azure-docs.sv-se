---
title: Översikt över insikter i Azure Monitor | Microsoft Docs
description: Insikter ger en anpassad övervaknings upplevelse i Azure Monitor för specifika program och tjänster. Den här artikeln innehåller en kort beskrivning av var och en av de insikter som är tillgängliga för närvarande.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 81d82ce79130513a2827090a98bfef192c9d7829
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795706"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Översikt över insikter i Azure Monitor
Insikter ger en anpassad övervaknings miljö för specifika program och tjänster. De lagrar data i [Azure Monitor data plattform](../platform/data-platform.md) och använder andra Azure Monitor funktioner för analys och avisering, men kan samla in ytterligare data och ge en unik användar upplevelse i Azure Portal. Få till gång till insikter från avsnittet om **insikter** på Azure Monitor-menyn i Azure Portal.

Följande avsnitt innehåller en kort beskrivning av de insikter som för närvarande är tillgängliga i Azure Monitor. Mer information om var och en finns i den detaljerade dokumentationen.

## <a name="application-insights"></a>Application Insights
Application Insights är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Det fungerar för program på en mängd olika plattformar, inklusive .NET, Node. js och Java-EE, lokalt, hybrid eller offentligt moln. Den integreras också med DevOps-processen och har anslutnings punkter till olika utvecklingsverktyg.

Se [Vad är Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar
Azure Monitor för behållare övervakar prestanda för behållar arbets belastningar som distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). Övervakning av behållare är avgörande, särskilt när du kör ett produktions kluster, i skala med flera program.

Se [Översikt över Azure Monitor för behållare](../insights/container-insights-overview.md).

![Azure Monitor för containrar](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor för resurs grupper (förhands granskning)
Azure Monitor för resurs grupper hjälper till att prioritering och diagnostisera eventuella problem som kan uppstå för enskilda resurser, samtidigt som den erbjuder kontexten och prestandan för resurs gruppen som helhet.

Se [övervaka resurs grupper med Azure Monitor (för hands version)](../insights/resource-group-insights.md).

![Azure Monitor för resurs grupper](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (för hands version)
Azure Monitor for VMs övervakar dina virtuella Azure-datorer (VM) och skalnings uppsättningar för virtuella datorer i stor skala. Den analyserar prestanda och hälsotillstånd för dina virtuella datorer med Windows och Linux, och övervakar deras processer och beroenden på andra resurser och i externa processer.

Se [Vad är Azure Monitor for VMS?](vminsights-overview.md)

![Azure Monitor för virtuella datorer](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-network-preview"></a>Azure Monitor för nätverk (för hands version)
Azure Monitor för nätverk ger en omfattande vy över hälsa och mått för alla nätverks resurser. Med den avancerade Sök funktionen kan du identifiera resurs beroenden, aktivera scenarier som att identifiera resurser som är värdar för din webbplats genom att helt enkelt söka efter namnet på din webbplats.

![Azure Monitor för nätverk](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om den [Azure Monitor data plattform](../platform/data-platform.md) som utnyttjas av insikter.
* Lär dig mer om de olika [data källor som används av Azure Monitor](../platform/data-sources.md) och de olika typer av data som samlas in av var och en av insikterna.
