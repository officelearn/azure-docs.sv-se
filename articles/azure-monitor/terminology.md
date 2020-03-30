---
title: Terminologiuppdateringar för Azure Monitor | Microsoft-dokument
description: Beskriver de senaste terminologiändringarna som gjorts i Azure-övervakningstjänster.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 0c5e1096b0780d2f9e50389f772e63344935012b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274143"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Namngivnings- och terminologiändringar i Azure Monitor
Betydande ändringar har nyligen gjorts i Azure Monitor, med olika tjänster konsolideras för att förenkla övervakningen för Azure-kunder. I den här artikeln beskrivs de senaste namn- och terminologiändringarna i Azure Monitor-dokumentationen.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Oktober 2019 - Diagnostiklogg till resurslogg
"Diagnostikloggar" ändrades till "resursloggar" för att bättre matcha vad som faktiskt samlas in. Termen "diagnostikinställningar" förblir densamma.  

## <a name="february-2019---log-analytics-terminology"></a>Februari 2019 - Log Analytics terminologi
Efter konsolideringen av olika tjänster under Azure Monitor tar vi nästa steg genom att ändra terminologin i vår dokumentation för att bättre beskriva Azure Monitor-tjänsten och dess olika komponenter. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor-loggdata lagras fortfarande i en Log Analytics-arbetsyta och samlas fortfarande in och analyseras av samma Log Analytics-tjänst, men vi ändrar termen _Logganalys_ på många ställen till _Azure Monitor-loggar_. Den här termen återspeglar bättre sin roll i Azure Monitor och ger bättre konsekvens med [mått i Azure Monitor](platform/data-platform-metrics.md).

Termen _logganalys_ gäller nu främst för sidan i Azure-portalen som används för att skriva och köra frågor och analysera loggdata. Det är den funktionella motsvarigheten till [mått explorer](platform/metrics-charts.md), som är den sida i Azure-portalen som används för att analysera måttdata.

### <a name="log-analytics-workspaces"></a>Log Analytics-arbetsytor
[Arbetsytor](platform/manage-access.md) som innehåller loggdata i Azure Monitor kallas fortfarande Log Analytics-arbetsytor. **Log Analytics-menyn** i Azure-portalen har bytt namn till **Log Analytics-arbetsytor** och är där du [skapar nya arbetsytor](learn/quick-create-workspace.md) och konfigurerar datakällor. Analysera dina loggar och andra övervakningsdata i **Azure Monitor** och konfigurera arbetsytan i **Log Analytics-arbetsytor**.

### <a name="management-solutions"></a>Hanteringslösningar
[Hanteringslösningar](insights/solutions.md) har bytt namn till _övervakningslösningar_, som bättre beskriver deras funktionalitet.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Augusti 2018 - Konsolidering av övervakningstjänster till Azure Monitor
Log Analytics och Application Insights har konsoliderats i Azure Monitor för att ge en enda integrerad upplevelse för övervakning av Azure-resurser och hybridmiljöer. Inga funktioner har tagits bort från dessa tjänster och användarna kan utföra samma scenarier som de alltid har slutfört utan förlust eller kompromettering av några funktioner.

Dokumentation för var och en av dessa tjänster har konsoliderats till en enda uppsättning innehåll för Azure Monitor. Detta kommer att hjälpa läsaren att hitta allt innehåll för ett visst övervakningsscenario på en enda plats i stället för att behöva referera till flera uppsättningar innehåll. I takt med att den konsoliderade tjänsten utvecklas kommer innehållet att bli mer integrerat.

Andra funktioner som ansågs vara en del av Logganalys, till exempel agenter och vyer, har också flyttats som funktioner i Azure Monitor. Deras funktionalitet har inte ändrats annat än potentiella förbättringar av deras upplevelse i Azure-portalen.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>April 2018 - Pensionering av operations management suite varumärke
Operations Management Suite (OMS) var en kombination av följande Azure-hanteringstjänster för licensiering:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Nya priser har införts för dessa tjänster](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), och OMS buntning är inte längre tillgänglig för nya kunder. Ingen av de tjänster som ingick i OMS har ändrats, förutom konsolideringen till Azure Monitor som beskrivs ovan. 




## <a name="next-steps"></a>Nästa steg

- Läs en [översikt över Azure Monitor](overview.md) som beskriver dess olika komponenter och funktioner.
- Läs mer om [övergången av OMS-portalen](../log-analytics/log-analytics-oms-portal-transition.md).
