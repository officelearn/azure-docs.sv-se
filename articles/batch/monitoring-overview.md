---
title: Övervaka Azure Batch | Microsoft Docs
description: Lär dig mer om Azures övervaknings tjänster, statistik, diagnostikloggar och andra övervakningsfunktioner för Azure Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: 3ac852b1d69c0e9fa224b449d6261eba6abaff32
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260955"
---
# <a name="monitor-batch-solutions"></a>Övervaka Batch-lösningar

Azure och batch-tjänsten tillhandahåller en mängd tjänster, verktyg och API: er för att övervaka dina batch-lösningar. I den här översikts artikeln får du hjälp att välja en övervaknings metod som passar dina behov.

En översikt över de Azure-komponenter och-tjänster som är tillgängliga för övervakning av Azure-resurser finns i [övervaka Azure-program och-resurser](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Övervakning på prenumerations nivå

På prenumerations nivå, som innehåller batch-konton, samlar [Azure aktivitets loggen](../azure-monitor/platform/activity-logs-overview.md) in drifts händelse data i [flera kategorier](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log).

För batch-konton är det specifikt att samla in händelser som rör skapande och borttagning av konton och nyckel hantering i aktivitets loggen.

Ett sätt att hämta händelser från aktivitets loggen är att använda Azure Portal. Klicka på **alla tjänster** > **aktivitets logg**. Du kan också fråga efter händelser med hjälp av Azure CLI, PowerShell-cmdletar eller Azure Monitor REST API. Du kan också exportera aktivitets loggen eller konfigurera [aktivitets logg aviseringar](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Övervakning av batch-konto-nivå

Övervaka varje batch-konto med hjälp av funktioner i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [Mät värden](../azure-monitor/platform/data-platform-metrics.md) och eventuella [diagnostiska loggar](../azure-monitor/platform/resource-logs-overview.md) för resurser som omfattas på samma nivå som ett batch-konto, till exempel pooler, jobb och uppgifter. Samla in och Använd dessa data manuellt eller program mässigt för att övervaka aktiviteter i ditt batch-konto och för att diagnosticera problem. Mer information finns i [batch-mått, aviseringar och loggar för diagnostisk utvärdering och övervakning](batch-diagnostics.md).
 
> [!NOTE]
> Mått är tillgängliga som standard i batch-kontot utan ytterligare konfiguration, och de har en historik på 30 dagar. Du måste aktivera diagnostikloggning för ett batch-konto och du kan debiteras ytterligare kostnader för att lagra eller bearbeta diagnostikdata. 

## <a name="batch-resource-monitoring"></a>Batch-resurs-övervakning

I batch-programmen använder du batch-API: erna för att övervaka eller fråga efter status för dina resurser, inklusive jobb, aktiviteter, noder och pooler. Exempel:

* [Räkna aktiviteter och Compute-noder efter tillstånd](batch-get-resource-counts.md)
* [Skapa frågor för att lista batch-resurser effektivt](batch-efficient-list-queries.md)
* [Skapa aktivitets beroenden](batch-task-dependencies.md)
* Använd en [Job Manager-aktivitet](/rest/api/batchservice/job/add#jobmanagertask)
* Övervaka [uppgifts status](/rest/api/batchservice/task/list#taskstate)
* Övervaka [nodens tillstånd](/rest/api/batchservice/computenode/list#computenodestate)
* Övervaka [poolens tillstånd](/rest/api/batchservice/pool/get#poolstate)
* Övervaka [pool användning i kontot](/rest/api/batchservice/pool/listusagemetrics)
* [Antal noder per tillstånd för poolen](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Prestanda räknare för virtuella datorer och program övervakning

* [Application Insights](../azure-monitor/app/app-insights-overview.md) är en Azure-tjänst som du kan använda för att övervaka tillgänglighet, prestanda och användning för dina batch-jobb och-aktiviteter. Du kan enkelt få prestanda räknare från datornoder (VM) och anpassad information för aktiviteter från de virtuella datorerna. 

  Ett exempel finns i [övervaka och felsöka ett batch .NET-program med Application Insights](monitor-application-insights.md) och det medföljande [kod exemplet](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Du kan debiteras ytterligare kostnader för att använda Application Insights. Se [pris alternativ](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett kostnads fritt, fristående klient verktyg med omfattande funktioner som hjälper dig att skapa, felsöka och övervaka Azure Batch program. Hämta ett [installationspaketet](https://azure.github.io/BatchExplorer/) för Mac, Linux eller Windows. Du kan också konfigurera batch-lösningen så att den [visar Application Insights data](https://github.com/Azure/batch-insights) , till exempel prestanda räknare för virtuella datorer i batch Explorer.


## <a name="next-steps"></a>Nästa steg

* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Lär dig mer om [diagnostisk loggning](batch-diagnostics.md) med batch.