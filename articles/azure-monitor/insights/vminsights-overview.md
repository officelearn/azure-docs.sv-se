---
title: Vad är Azure Monitor for VMs?
description: Översikt över Azure Monitor for VMs, som övervakar hälso tillstånd och prestanda för virtuella Azure-datorer och identifierar och mappar program komponenter och deras beroenden automatiskt.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: f9ad39b88ad2212ea2cdceb40e61fbc0a2d1a764
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320501"
---
# <a name="what-is-azure-monitor-for-vms"></a>Vad är Azure Monitor for VMs?

Azure Monitor for VMs övervakar de virtuella datorernas prestanda och hälsa och skalnings uppsättningar för virtuella datorer, inklusive processer som körs och beroenden på andra resurser. Det kan hjälpa till att leverera förutsägbar prestanda och tillgänglighet för viktiga program genom att identifiera Flask halsar och nätverks problem och kan också hjälpa dig att förstå om ett problem är relaterat till andra beroenden.

Azure Monitor for VMs stöder Windows-och Linux-operativ system på följande:

- Virtuella Azure-datorer
- Azure Virtual Machine Scale Sets
- Virtuella hybrid datorer som är anslutna till Azure Arc
- Lokala virtuella datorer
- Virtuella datorer som finns i en annan moln miljö
  


>[!NOTE]
>Vi har nyligen [lanserat ändringar](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) som vi gör i hälso funktionen baserat på feedback vi har fått från våra kunder med offentlig för hands version. Med tanke på antalet ändringar vi kommer att göra, kommer vi att sluta erbjuda hälso funktionen för nya kunder. Befintliga kunder kan fortsätta att använda hälso funktionen. Mer information finns i [vanliga frågor och svar om allmän tillgänglighet](vminsights-ga-release-faq.md).  


Azure Monitor for VMs lagrar data i Azure Monitor loggar som gör det möjligt att leverera kraftfull agg regering och filtrering och analysera data trender över tid. Du kan visa dessa data i en enda virtuell dator från den virtuella datorn direkt, eller så kan du använda Azure Monitor för att leverera en sammanställd vy över flera virtuella datorer.

![Den virtuella datorns insikter perspektiv i Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Prissättning
Det finns ingen direkt kostnad för Azure Monitor for VMs, men du debiteras för aktiviteten på arbets ytan Log Analytics. Baserat på prissättningen som publiceras på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/)faktureras Azure Monitor for VMS för:

- Data som matats in från agenter och lagras i arbets ytan.
- Aviserings regler baserade på logg-och hälso data.
- Meddelanden som skickas från varnings regler.

Logg storleken varierar med sträng längden för prestanda räknare och kan öka med antalet logiska diskar och nätverkskort som allokeras till den virtuella datorn. Om du redan använder Tjänstkarta, är den enda ändringen som visas de ytterligare prestanda data som skickas till `InsightsMetrics` data typen Azure Monitor.


## <a name="configuring-azure-monitor-for-vms"></a>Konfigurera Azure Monitor for VMs
Stegen för att konfigurera Azure Monitor for VMs är följande. Följ varje länk för detaljerad vägledning om varje steg:

- [Skapa Log Analytics arbets yta.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Lägg till VMInsights-lösning till arbets ytan.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installera agenter på den virtuella datorn och skalnings uppsättningen för den virtuella datorn som ska övervakas.](vminsights-enable-overview.md)



## <a name="next-steps"></a>Nästa steg

- Se [distribuera Azure Monitor for VMS](vminsights-enable-overview.md) för krav och metoder för att aktivera övervakning för dina virtuella datorer.

